---
title: Route Optimization to Visit Every Big Ten Team at Home
author: 'Emaly Vatne'
date: '2025-06-17'
slug: routeoptimization
categories: []
subtitle: ''
summary: 'This post is about applying the "traveling saleman problem and linear programming to the Big Ten football schedule.'
authors: []
lastmod: '2025-06-17T20:57:45-05:00'
projects: []
---

Managing and mitigating the negative effects of long-haul travel is a timely challenge facing collegiate athletics departments. This was particularly evident to me when I was watching the women's tennis NCAA tournament draw in the spring. There were a remarkable number of teams that had to travel what I perceived to be substantial distances for their first round matches. It did not appear to me that the optimal set of match-ups was identified to minimize the distance traveled while maintaining the appropriateness (e.g., in terms of RPI or other indicators of competitive level) of the two teams competing. Ultimately, this is one of a few examples I can think of where a scheduling and route optimization data science problem would be relevant.

While building a 64-team NCAA women's tennis tournament would be a fun project, it would likely take quite a bit of bandwidth and computational power. I thought it would be good to start to demonstrate a simpler example of a scheduling and route optimization problem. To do this, I decided to start by taking the 2025 Big Ten football schedule and try to identify the route someone would need to take to visit every Big Ten football stadium for a home game while minimizing the total distance traveled. 

The full project and the code to do all of the subsequently described web-scraping, cleaning, and analysis can be found at my github: https://github.com/emalyvatne/bigtenfootball_routeoptimization

#### Big Ten Football Context

Starting this data science problem involves collecting:  

1. The exact location (e.g., latitude and longitude) of each Big Ten stadium
2. The Big Ten football schedule

##### Big Ten School Details and Distance Matrix

The first assumption we will make in this problem is that the stadium is located in a negligible distance from the latitude and longitude of the city that the university is listed in. This also means we are not accounting for certain things such as mode of travel (e.g., driving versus flying) and ancillary details such as distance between a given airport and that stadium. While these things will inevitably add up when traveling to 18 schools, we are overlooking these details for the sake of this problem. In a more comprehensive approach to this question, I would like to have included the flight tracker API and Google Maps API to account for more accurate estimates of distances traveled. With all of that being said, we'll proceed with this question by obtaining the latitude and longitude of the Big Ten institutions by using a simple webscraper to collect this information off of Wikipedia: 

```
import pandas as pd
from bs4 import BeautifulSoup
import requests
from lat_lon_parser import parse
pd.options.display.max_columns = None
import os
 
# get URL
page = requests.get("https://en.wikipedia.org/wiki/Big_Ten_Conference")
 
# scrape webpage
soup = BeautifulSoup(page.content, 'html.parser')
 
table = soup.findAll('table')[1]
tbody = table.find('tbody')
data = []
# iterate through each stadium listing in the table
for i, row in enumerate(tbody.findAll('tr')):

    # ignore header row
    if i == 0: continue
    stadium = row.find('th')
    link = stadium.find('a')['href']

    # click into the stadium wikipedia page and extract latitude and longitude coordinates
    soup2 = BeautifulSoup(requests.get(f'https://en.wikipedia.org{link}').content, 'html.parser')
    loc = soup2.find('span', class_='geo-dms')
    lat = parse(loc.find('span', class_='latitude').getText())
    lng = parse(loc.find('span', class_='longitude').getText())
    data.append([row.find('th').getText().strip(), lat, lng, *[col.getText().strip() for col in row.findAll('td')]])

# assemble dataframe
COLUMNS = ['Institution', 'Latitude', 'Longitude', 'Location', 'Founded', 'Joined', 'Type', 'Enrollment',
           'Endowment', 'Nickname', 'Colors.Blank']
full_wiki_locations = pd.DataFrame(data, columns = COLUMNS)

# cleaned stadiums
clean_locations = full_wiki_locations[['Institution', 'Latitude', 'Longitude', 'Location']]
```

Next, we need to create a matrix of distances between each of the 18 schools. We will leverage the `geopy` package that provides the geodesic distances between two geo-coordinates, or the shortest path between two points on a curved surface. 

Here is the code for those computations:

```
import pandas as pd
from geopy.distance import geodesic
import os

# join each team name with abbreviations table to get tricode
df = pd.read_csv(f"{output_dir}/arena_info.csv")
TEAMS = sorted(df['Institution'].to_list())


# group latitude and longitude info into coordinate tuples
df['Coords'] = df.apply(lambda x: (x['Latitude'], x['Longitude']), axis=1)

# manipulate object type from dataframe -> dictionary
coords_dict = df.set_index('Institution')[['Coords']].to_dict(orient='index')

# compute distance between each arena
miles = []
for t in TEAMS:
    for tt in TEAMS:
        distance = geodesic(coords_dict[t]['Coords'], coords_dict[tt]['Coords']).miles
        miles.append([t, tt, distance])
mile_df = pd.DataFrame(miles, columns = ['src', 'dest', 'miles'])

# compute distance between each arena
miles = []
for t in TEAMS:
    distance = [geodesic(coords_dict[t]['Coords'], coords_dict[tt]['Coords']).miles for tt in TEAMS]
    miles.append([t, *distance])
mile_df_wide = pd.DataFrame(miles, columns = ['', *TEAMS])
```
I threw this matrix into power bi for a quick vizualization of distances between schools. Intuitively, the west coast additions consistently travel absurd distances. (The diagonal line of 0s is where that school is lined up with itself while green represents a smaller distance and red is larger).

![Big Ten Distance Matrix](distance_matrix.png)

#### Big Ten Schedule

The next thing we need is the Big Ten football schedule. For the sake of simplicity, I'm using the 2024 fall schedule since it's on College Football Reference. Here is the code I used to scrape the schedule:

```
import pandas as pd
from bs4 import BeautifulSoup
import requests
pd.options.display.max_columns = None
import os
 
# get URL
page = requests.get("https://www.sports-reference.com/cfb/conferences/big-ten/2024-schedule.html")
 
# scrape webpage
soup = BeautifulSoup(page.content, 'html.parser')
 
# grab table values
table = soup.find('table', id="schedule")
tbody = table.find('tbody')
data = []
for i, row in enumerate(tbody.findAll('tr')):
    if row.find('th').getText().strip() == 'Rk':
        continue
    data.append([row.find('th').getText().strip(), *[col.getText().strip() for col in row.findAll('td')]])
df = pd.DataFrame(data, columns = ['Rk', 'Date', 'Time', 'Day', 'Winner', 'Pts', '', 'Loser', 'Pts', 'Notes'])

# preview and save to csv
print(f'There were {df.shape[0]} Big Ten football games this season')
```

From the output, there were 83 Big Ten Football games last season, which is a good reason why approaching this problem manually would be naive. While we could probably build a decent route, it would be a waste of bandwidth and we could be more accurate by approaching this as a datascience problem.

Now that we have the data inputs, we can start to build and run the model!!

#### Solving the Problem

We have 18 locations to visit and 83 games to choose from. This means there is an 18-stop tour and would result in n factorial possible routes where n is the number of locations. 18! is 6.4023737e+15 possible routes(!!!). 

#### Data Science Background

The challenge of planning a route that visits every Big Ten football stadium exactly once, while minimizing total distance traveled, is a classic example of a combinatorial optimization problem known as the Traveling Salesman Problem (TSP). At its core, the TSP asks: Given a set of cities and the distances between them, what is the shortest possible path that visits each city once and returns to the starting point?

The TSP is known to be NP-hard, meaning that as the number of locations increases, the number of possible routes explodes factorially—making a brute-force approach computationally impractical. For example, with 18 stadiums to visit, there are over 6 quadrillion potential route combinations. Because of this, data science tools like linear programming or heuristic optimization algorithms become essential for finding efficient solutions in a reasonable amount of time.

By framing this travel task as a TSP and applying algorithmic solutions, we can move beyond intuition and guesswork to determine an optimized, data-driven route through the Big Ten.

There are a few difference technical approaches to this problem, but describing and defining them is beyond the scope of this article. For this use case, I will apply the Miller-Tucker-Zemlin (MTZ) formulation because it appears to be well-known for the TSP. This approach allows for additional constraints and variables in addition to the conventional TSP. The code below presents the development and running of the model:

```
import os
import pandas as pd
# pd.options.display.max_rows = None
from pulp import *

main_dir = os.getcwd()
output_dir = f"{main_dir}/outputs"

schedule = pd.read_csv(f"{output_dir}/schedule.csv")
schedule = schedule.rename(columns={"Unnamed: 7": "LocationDesignation"})
date_df = schedule.set_index('Date')
mile_matrix_wide = pd.read_csv(f"{output_dir}/mile_matrix_wide.csv")

mile_matrix_wide = mile_matrix_wide.rename(columns={"Unnamed: 0": "source"})
mile_matrix_long = mile_matrix_wide.melt(id_vars="source", var_name="destination", value_name="miles")

# ----- map mile matrix school names to the college football schedule -----
matrixlist_to_team = {
    'University of Illinois Urbana-Champaign': 'Illinois',
    'Indiana University Bloomington': 'Indiana',
    'University of Iowa': 'Iowa',
    'University of Maryland, College Park': 'Maryland',
    'University of Michigan': 'Michigan',
    'Michigan State University': 'MichiganState',
    'University of Minnesota Twin Cities': 'Minnesota',
    'University of Nebraska-Lincoln': 'Nebraska',
    'Northwestern University': 'Northwestern',
    'The Ohio State University': 'OhioState',
    'University of Oregon': 'Oregon',
    'Pennsylvania State University': 'PennState',
    'Purdue University': 'Purdue',
    'Rutgers, The State University of New Jersey, New Brunswick': 'Rutgers',
    'University of Southern California': 'SouthernCalifornia',
    'University of California, Los Angeles': 'UCLA',
    'University of Washington': 'Washington',
    'University of Wisconsin-Madison': 'Wisconsin'
}
mile_matrix_long['source'] = mile_matrix_long['source'].map(matrixlist_to_team)
mile_matrix_long['destination'] = mile_matrix_long['destination'].map(matrixlist_to_team)

# ----- clean the schedule -----
# rename columns
schedule.columns = ['Unnamed', 'Rk', 'Date', 'Time', 'Day', 'Winner', 'Pts', 'LocationDesignation', 'Loser', 'Pts.1', 'Notes']
# create home/away assignment
schedule['Home'] = schedule.apply(
    lambda row: row['Loser'] if row['LocationDesignation'] == '@' else row['Winner'],
    axis=1
)
schedule['Away'] = schedule.apply(
    lambda row: row['Winner'] if row['LocationDesignation'] == '@' else row['Loser'],
    axis=1
)

# assign points
schedule['HomePts'] = schedule.apply(
    lambda row: row['Pts.1'] if row['LocationDesignation'] == '@' else row['Pts'],
    axis=1
)
schedule['AwayPts'] = schedule.apply(
    lambda row: row['Pts'] if row['LocationDesignation'] == '@' else row['Pts.1'],
    axis=1
)

# select and order desired columns
schedule = schedule[['Rk', 'Date', 'Time', 'Day', 'Home', 'HomePts', 'Away', 'AwayPts', 'Notes']]
# clean the Home and Away column to remove rankings
schedule['Home'] = schedule['Home'].str.replace('\xa0', '', regex=False).str.replace(r"[ ()\d]", "", regex=True)
schedule['Away'] = schedule['Away'].str.replace('\xa0', '', regex=False).str.replace(r"[ ()\d]", "", regex=True)

# There are 17 Big Ten teams and 17 locations to visit
TEAMS = ['Home', *sorted(list(schedule['Home'].unique()))]

# There are 25 game dates [len(schedule['Date'].unique())] in the 2024 season
DATES = [0, *list(schedule['Date'].unique())]

# Define Problem  

prob = LpProblem("Matrix Problem",LpMinimize)
# Creating a Set of Variables
# date, source, desetination
choices = LpVariable.dicts("Interview",(DATES,TEAMS,TEAMS), cat='Binary')

# Added arbitrary objective function
prob += 0, "Arbitrary Objective Function"

# ---------------- CONSTRAINTS ------------------------

# ensure each arena is departed from exactly 1 time
for t2 in TEAMS:
    prob += lpSum([choices[d][t1][t2] for d in DATES[1:] for t1 in TEAMS]) == 1, ""

# ensure each arena is visited exactly 1 time
for t1 in TEAMS:
    prob += lpSum([choices[d][t1][t2] for d in DATES[1:] for t2 in TEAMS]) == 1, ""

# minimize total number of visits
prob += lpSum([choices[d][t1][t2] for d in DATES[1:] for t1 in TEAMS for t2 in TEAMS if t1 != t2]) <= 33

# start from home and end at home
prob +=lpSum([choices[d]['Home'][t2] for t2 in TEAMS[1:] for d in DATES[1:4]]) == 1
prob +=lpSum([choices[d][t1]['Home'] for t1 in TEAMS[1:] for d in DATES[-1:]]) == 1

# make sure visits are played on home team dates
for d in DATES[1:]:
    for t2 in TEAMS[1:]:
        for t1 in TEAMS:
            if schedule[(schedule['Date'] == d) & (schedule['Home'] == t2)].shape[0] == 0:
                prob += choices[d][t1][t2] == 0, ""

    prob += lpSum([choices[d][t1][t2] for t1 in TEAMS for t2 in TEAMS]) <= 1, ""

for d in DATES:
    for t1 in TEAMS[1:]:
        for t2 in TEAMS[1:]:
            if t1 == t2:
                prob += choices[d][t1][t2] == 0, ""

# Ensure that visits are sequential by dates
for i, d in enumerate(DATES[1:-1]):
    for t1 in TEAMS:
        for t2 in TEAMS[1:]:
            if t1 != t2:
                prob += choices[DATES[i]][t1][t2] <= lpSum([choices[d2][t2][t3] for d2 in DATES[i+1:i+4] for t3 in TEAMS if t3 != t2]), f""

for i, d in enumerate(DATES[2:]):
    for t1 in TEAMS[1:]:
        for t2 in TEAMS:
            if t1 != t2:
                prob += choices[DATES[i]][t1][t2] <= lpSum([choices[d2][t3][t1] for d2 in DATES[i-4:i] for t3 in TEAMS if t3 != t1]), f""

# Minimize travel distance for each segment
if (t1, t2) in mile_matrix_long.index:
    miles = mile_matrix_long.loc[(t1, t2), 'miles']  
    if isinstance(miles, pd.Series):
        miles = miles.iloc[0]
        
    prob += choices[d][t1][t2] * miles <= 1100

    # 1.  Build a truly unique MultiIndex (strip stray spaces/ \xa0 first)
mile_matrix_long = (
    mile_matrix_long
        .assign(source=lambda d: d["source"].str.strip(),      # <- kills \xa0 & spaces
                destination=lambda d: d["destination"].str.strip())
        .drop_duplicates(["source", "destination"])            # guarantee uniqueness
        .set_index(["source", "destination"])
        .sort_index()
)

# 2.  Convert the miles column to a plain Python dict for O(1) scalar lookup
distance_lookup = mile_matrix_long["miles"].to_dict()

# 3.  Build the algebraic expression once so you can re-use it
total_distance = lpSum(
    choices[d][t1][t2] * distance_lookup[(t1, t2)]
    for d  in DATES[1:]            # skip dummy element 0
    for t1 in TEAMS[1:]            # "
    for t2 in TEAMS[1:] if t1 != t2
)

# 4.  Tell PuLP what you actually want
prob += total_distance, "MinimizeTravelDistance"   # <- the objective
prob += total_distance <= 14_000, "TravelDistanceLimit"  # <- optional hard cap

# 5.  Solve
prob.solve()
print("Status:", LpStatus[prob.status])
print("Best total miles:", value(total_distance))

debug = ""
if LpStatus[prob.status] == 'Optimal':
    print('solved problem')
    matrix = []
    for t2 in TEAMS:
        row = []
        for t1 in TEAMS:
            value = 0
            for d in DATES:
                if choices[d][t1][t2].varValue == 1:
                    value = d
            row.append(value)

        matrix.append(row)

    matrix_df = pd.DataFrame(matrix, columns=TEAMS)
    matrix_df.index = TEAMS

else:
    print('Fail')

# ----- clean the final schedule up a little bit -----

data = []
df = matrix_df.copy()
solution_long = df.melt(id_vars="destination", var_name="source", value_name="date")
solution_long = solution_long[solution_long['date'] != '0']
solution_long['date'] = (
    pd.to_datetime(
        solution_long['date'].str.strip(),
        format='%b %d, %Y',
        errors='coerce'
    )
)
solution_long = solution_long.sort_values('date', ascending=True)
solution_long = solution_long[["source", "destination", "date"]]
```

The contraints of this problem are the (1) each stadium is visited once, (2) stadiums are only visited when there is a home game at that location, (3) the visits must be in chronological order of date, and (4) distances traveled must be minimized and subtours should be avoided.

#### Results

The final schedule on the route is shown below:

![Final Schedule](final_schedule.png)

![Final Travel Route](final_travel_route.png)


