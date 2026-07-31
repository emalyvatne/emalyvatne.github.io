---
title: The Expected Value is Not Always the Most Likely or Most Probable Outcome
author: 'Emaly Vatne'
date: '2026-07-31'
slug: xGRantPtOne
categories: []
subtitle: 'How I Think We're Misinterpreting xG: Part 1'
summary: ''
authors: []
lastmod: '2025-07-31T20:57:45-05:00'
projects: []
---

### Purpose 

Expected goals (xG) is now one of football's most commonly used metrics, but I thiink its widespread adoption has diluted its original meaning and purpose. I've actually been thinking a lot about how xG is discussed and interpreted, but a couple of recent conversations have prompted me to organize my thoughts. Therefore, the purpose of this post (which will almost certainly become a series) is to revisit the original definition of xG and statistically how it has become slightly corrupted. 

### Brief background 

xG is often used to describe the quality of a goal scoring chance. I'm not particularly interested in evaluating that statement, but I want to start this article by revisiting xG's true, original definition. xG is the name of models that estimate the likelihood of a shot resulting in a goal based on contextual features such as shot distance, angle, body part used, etc. It is outside of the scope of this article to discuss how xG models are built (e.g., logistic regression, xGBoost, etc), but I will probably discuss that in another post in this series because it is very relevant. However, for the purpose of this discussion, it is just important to know that xG estimates the likelihood of a shot resulting in a goal based on a set of predictor variables and the resulting probability value ranges from (0,1) with a 0 reflecting no chance of scoring and a 1 reflecting no chance of missing and all potential values in between. 

### Where it starts to go wrong

Imagine that a player who shoots from the PK spot in the run of play and has the opposition's goalkeeper and 2 defenders between her and the goal. Let's assume that the xG model that we're using (different providers will have slightly different results*) estimates that the likelihood of scoring is 0.6. A probability value is provided by the xG model for all shots in a match. Where xG starts to get misused, in my opinion, is rooted in statistical misinterpretations and extrapolations beyond what the data and model are actually saying.

#### A little statistics; sorry

Again, for any single shot, such as the one described above or any other, the estimated xG value is simply an estimated probability of scoring.  

For example:

| Shot | xG   |
|------|------|
| 1    | 0.45 |
| 2    | 0.20 |
| 3    | 0.08 |

Each shot can be modeled as a *Bernoulli random variable*, which is a discrete random variable with two possible outcomes: 1 (success) and 0 (failure) and can be modeled as:

$$Y_i \sim \text{Bernoulli}(p_i)$$

where:

- $Y_i = 1$ if the shot is scored, $Y_i = 0$ otherwise
- $p_i = \text{xG}_i$

The expected value of that shot is its success (aka scoring) probability:

$$E[Y_i] = p_i$$

#### **The issue**

If there are *n* shots across a match, the xG values for those *n* shots for a team across a match (or season or period of the season) are often summed and this is indeed statistically appropriate (using the linearity of expectation). So the issue is not that we're providing a sum of the xGs across a match. Thus, *the sum of xG is the expected value for the number of goals* (assuming the individual shot probabilities are correctly estimated of course).

### HOWEVER

## The Expected Value is Not Always the Most Likely or Probable Outcome 

The error is in the interpretation of this sum. Assuming 
	*2.3 xG = we should have scored 2 or 3 goals is incorrect because this is not what the data are saying*
	
*Expected value is not necessarily the most likely outcome*

Consider an example with a fair die. If you roll it, the expected outcome is:

E[X]= (1+2+3+4+5+6)/6 = 21/6 = 3.5

This number represents the long-term average outcome if you roll the die many times, but obviously you can never actually roll a 3.5. Similarly, a team with 2.3 xG is not necessarily "supposed" to score either 2 or 3 goals in a particular match; instead, just as 3.5 describes the average of many die rolls rather than any single roll (since that literally cannot happen), a 2.3 xG describes the average outcome across many hypothetical replays of that match, not the specific result you should *always* expect to see. It is important to note that xG , the expected value, can and may often align with the value that is observed, but still this misinterpretation of the value is a result of ignoring the uncertainty and distribution of probabilities for different potential outcomes such as how many goals are scored in a match or what number you roll with a fair die.

#### More on distributions

The number of total goals that a team scores follow a Poisson binomial distribution because each shot has a different scoring probability. A Poisson binomial distribution is a discrete probability distribution for the sum of independent yes/no experiments where each trial has its own **unique** success probability (which extends the standard binomial distribution, which requires every trial to share the exact same chance of success such as flipping a coin multiple times)


Consider another example in which a team has 5 shots with the following xGs assigned to them:

[0.70, 0.50, 0.40, 0.30, 0.40]
 
The total xG again = 2.3

*However*, there are 6 potential outcomes from the team taking 5 shots:  
- 0 goals 
- 1 goal 
- 2 goals 
- 3 goals 
- 4 goals 
- 5 goals 

The expected value is 2.3, but the probability of scoring exactly 2 goals might only be around 27%, with substantial probabilities for 1, 3, or even 0 and 4 goals depending on the uncertainty around the likelihood of converting shots into goals. Therefore, saying "They should have scored 2-3 goals from an xG of 2.3" is a naive conclusion to draw from xG. Again, it's entirely possible that this expected value is close to what is observed. In fact, this is probably what has led to xG so often being misinterpreted as the most likely outcome instead of the expected value. 

Here is another example:  

Consider two teams playing in a single match.  

Team A has ten shots of 0.20 xG and thus has a total xG = 2.0.

Team B has:  
- one shot of 0.95. 
- one shot of 0.95. 
- eighteen shots of 0.005. 

And also has a total xG ≈ 2.0. 

In this scenario, but teams have identical total expected goals, but the distributions of potential outcomes are very different. Team A has many moderate-probability chances. Team B is very likely to score one or two goals because of the two very high-quality chances. Only reporting on total xG alone does not distinguish these cases. 

### A more informed approach

Pretty much anyone at this point who will listen to met yap about sport science, and more specifically, data science/analysis applied to sport, will hear my spiel on the importance of quantifying and communicating uncertainty. How we use xG is no exception to this. With regards to how we quantify the number of goals scored in a match, a better approach may be reporting entire predicted distribution of goals scored based on the shot probabilities instead of the expected value. Revisiting the example with a team that took 5 shots in a game, perhaps the predicted distribution of goals scored is something like:

| Goals | Probability   |
|------|------|
| 0    | 19% |
| 1    | 35% |
| 2    | 29% |
| 3    | 11% |
| 4    | 4% |
| 5    | 2% |

And in this example, it is most likely that the team scores only one goal from the 5 shots despite the expected value being 2.3. (these values were made up for the sake of the example so they might not actually reflect true probabilities)

### Future directions 

I believe that xG developed its misinterpretation from often being close to the observed outcome in other (namely men's) leagues. This and other hypotheses will be addressed in subsequent posts. Specifically, I will lean on other, similar work such as Lago-Peñas (2026) to identify the most probable number of goals scored for teams, evaluate how that compares to xG in the NWSL vs MLS, and then reconstruct the league tables based on the most likely number of goals scored (and therefore match outcome).

### References 

Lago-Peñas, C., Feng, R., Zhang, Y., Dong, W., Gong, B., & Gómez-Ruano, M.-Á. (2026). A probabilistic and dynamic reformulation of expected goals (xG): Methodological advances and modelling perspectives. Intelligent Sports and Health, 2(3), 151–154. https://doi.org/10.1016/j.ish.2026.05.001


