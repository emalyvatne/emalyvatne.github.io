---
title: Comparison of Sleep Monitoring Data using EEG and Commercial Wearable Devices
author: 'Emaly Vatne'
date: '2023-11-22'
slug: COTS-wearable-sleep-vs-EEG
categories: []
subtitle: ''
summary: 'The purpose of this post is to define and demonstrate the statistical analysis techniques used to compare the data collected from a commercial wearable device to gold-standards. In this example, I use my own personal sleep data across 30 nights collected from an electroencephalography headband and two commercial wearables. I present the different analysis and visualization approaches as well as their results using R and RShiny.'
authors: []
lastmod: '2023-11-22T20:57:45-05:00'
projects: []
---

### Routine Sleep Monitoring in Athletes

The value of sleep for optimal health and performance is well described (O’Donnell et al., 2018; Walsh et al., 2021; Watson, 2017). A recent systematic review and meta-analysis of 77 studies with a total of 959 participants found that acute sleep loss had a significant negative impact on anaerobic power, speed/power endurance, high-intensity interval exercise, strength, strength-endurance, and skill performance (Craven et al., 2022). However, athletes, especially collegiate student-athletes, commonly cite poor and inadequate sleep quantity and quality (Mah et al., 2018; Merfeld et al., 2022). Practitioners in applied sport environments have attempted to monitor sleep and sleep behaviors to target education interventions and understand sleep among their athletes. Wearable devices are now commonly deployed for the measurement of routine sleep in athletes as a result of technological advancements (Driller et al., 2023). Commercial-off-the-shelf (COTS) wearable devices offer a mode of measuring sleep that addresses potential under- or overestimation of sleep that resulted from self-reporting (Carter et al., 2020) and also can be less onerous on athletes than asking them to do something like complete a sleep survey every day. Despite this, COTS wearable devices may also present inaccuracies or poor reliability. It is important that practitioners are aware of the limitations of the wearable devices that are deployed in their environment. Polysomnography (PSG) is the gold-standard in scientific research for measuring sleep, but this method is invasive and not practical in applied environments. Recent investigations have described how COTS wearable devices compare to PSG (Chinoy et al., 2022; Lee et al., 2018; Stone et al., 2021; Svensson et al., 2019). While these are the studies that I reference and would recommend referencing for choosing devices and key performance indicators from the devices, I wanted to do a personal comparison of my wearable devices to electroencephalography (EEG) using my own sleep data (please don’t judge my sleep). EEG has been identified as an alternative to PSG for the measurement of sleep (Arnal et al., 2020).

Therefore, this post is a comparison of two common COTS wearable devices to EEG using my own sleep data!! The data analysis is described within this post but can be found at [this link](https://emalvat.shinyapps.io/COTS_wearables_sleep_comparison/). Again, this is not meant to be referenced for choosing devices - I would recommend reading the following articles instead:

1.  Chinoy, E. D., Cuellar, J. A., Jameson, J. T., & Markwald, R. R. (2022). Performance of Four Commercial Wearable Sleep-Tracking
    Devices Tested Under Unrestricted Conditions at Home in Healthy
    Young Adults. Nature and Science of Sleep, 14, 493–516.
    <https://doi.org/10.2147/NSS.S348795>

2.  Lee, J.-M., Byun, W., Keill, A., Dinkel, D., & Seo, Y. (2018).
    Comparison of Wearable Trackers’ Ability to Estimate Sleep.
    International Journal of Environmental Research and Public Health,
    15(6), Article 6. <https://doi.org/10.3390/ijerph15061265>

3.  Stone, J. D., Ulman, H. K., Tran, K., Thompson, A. G., Halter, M.
    D., Ramadan, J. H., Stephenson, M., Finomore, V. S., Galster, S. M.,
    Rezai, A. R., & Hagen, J. A. (2021). Assessing the Accuracy of
    Popular Commercial Technologies That Measure Resting Heart Rate and
    Heart Rate Variability. Frontiers in Sports and Active Living,
    3, 585870. <https://doi.org/10.3389/fspor.2021.585870>

4.  Svensson, T., Chung, U., Tokuno, S., Nakamura, M., & Svensson, A. K.
    (2019). A validation study of a consumer wearable sleep tracker
    compared to a portable EEG system in naturalistic conditions.
    Journal of Psychosomatic Research, 126, 109822.
    <https://doi.org/10.1016/j.jpsychores.2019.109822>

5.  Miller, D. J., Sargent, C., & Roach, G. D. (2022). A Validation of
    Six Wearable Devices for Estimating Sleep, Heart Rate and Heart Rate
    Variability in Healthy Adults. Sensors (Basel, Switzerland),
    22(16), 6317. <https://doi.org/10.3390/s22166317>

This article is more about the statistical analysis approaches for comparing a device’s data to values from a gold-standard. And kind of just for fun for me :)

### The Data

I have a CSV file that contains 30 nights of sleep data collected from Dreem 2 EEG, Oura Ring Generation 3, and Whoop. For the purpose of this post, the Dreem 2 EEG device is serving as the gold-standard. For each device, I am looking at the number of hours and minutes of total, deep, REM, and light sleep as well as the time spent awake. I put each device on within at least 30 minutes of going to sleep and started a sleep session for the Dreem 2 and Whoop device. For the Oura Ring, the sleep session was cropped to match the start and stop times of the Dreem 2 sleep session on the subsequent morning. These methods replicate methods that have been previously described.

Here is my code for loading and looking at the structure of the data:

    library(tidyverse)
    sleep_df <- read.csv("sleep-data.csv", check.names = FALSE)
    str(sleep_df)

    ## 'data.frame':    30 obs. of  31 variables:
    ##  $ Date                     : chr  "1/29/2023" "1/30/2023" "1/31/2023" "2/1/2023" ...
    ##  $ EEG_sleep_duration_hour  : int  6 6 5 7 8 8 8 7 9 7 ...
    ##  $ EEG_sleep_duration_min   : int  59 22 29 29 40 55 28 44 5 6 ...
    ##  $ EEG_deep_hour            : int  1 1 1 1 1 1 0 1 1 1 ...
    ##  $ EEG_deep_min             : int  18 7 14 14 14 3 59 13 40 14 ...
    ##  $ EEG_REM_hour             : int  1 2 0 2 2 2 3 2 3 2 ...
    ##  $ EEG_REM_min              : int  52 7 38 41 59 46 12 31 18 26 ...
    ##  $ EEG_light_hour           : int  3 3 3 3 4 5 4 4 4 3 ...
    ##  $ EEG_light_min            : int  53 12 43 45 30 8 22 2 11 28 ...
    ##  $ EEG_wake_hour            : int  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ EEG_wake_min             : int  14 31 5 10 10 15 15 28 4 12 ...
    ##  $ oura_sleep_duration_hour : int  6 6 8 7 8 8 8 7 8 6 ...
    ##  $ oura_sleep_duration_min  : int  41 24 21 18 31 26 4 26 29 59 ...
    ##  $ oura_deep_hour           : int  2 1 3 2 3 1 2 2 2 2 ...
    ##  $ oura_deep_min            : int  57 55 18 49 8 53 15 31 52 15 ...
    ##  $ oura_REM_hour            : int  0 1 1 0 1 1 1 0 1 1 ...
    ##  $ oura_REM_min             : int  34 22 2 59 19 42 30 25 49 31 ...
    ##  $ oura_light_hour          : int  0 3 4 3 4 4 4 4 3 3 ...
    ##  $ oura_light_min           : int  30 7 2 30 5 51 20 30 49 14 ...
    ##  $ oura_wake_hour           : int  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ oura_wake_min            : int  57 29 50 32 20 43 40 37 47 23 ...
    ##  $ whoop_sleep_duration_hour: int  6 6 7 7 7 8 8 7 8 6 ...
    ##  $ whoop_sleep_duration_min : int  40 17 50 5 43 20 9 16 24 38 ...
    ##  $ whoop_deep_hour          : int  1 1 1 1 1 1 1 1 2 1 ...
    ##  $ whoop_deep_min           : int  25 7 26 40 43 46 34 46 25 11 ...
    ##  $ whoop_REM_hour           : int  1 1 1 2 2 1 0 0 1 0 ...
    ##  $ whoop_REM_min            : int  53 29 57 6 2 50 47 42 59 49 ...
    ##  $ whoop_light_hour         : int  3 3 4 3 3 4 5 4 4 4 ...
    ##  $ whoop_light_min          : int  22 41 27 19 58 44 48 48 0 38 ...
    ##  $ whoop_wake_hour          : int  0 0 1 0 0 0 0 0 0 0 ...
    ##  $ whoop_wake_min           : int  31 34 5 44 52 54 37 47 45 38 ...

It’s always good to take a look at the structure of the data. Main thing is I want to make sure of is the data is in wide format and each column is the right type. As you can see from these results, hour and minute is separated and presented as an integer. I will clean this up a little bit using `dplyr` so that we have each sleep stage duration in a single column with decimal places from minutes/60 representing the minutes column.

    sleep_summarized <- sleep_df %>% 
      dplyr::group_by(
        Date
      ) %>% 
      summarize(
        "EEG_total_duration" = round(EEG_sleep_duration_hour + (EEG_sleep_duration_min/60), digits = 3),
        "EEG_deep_duration" = round(EEG_deep_hour + (EEG_deep_min/60), digits = 3),
        "EEG_REM_duration" = round(EEG_REM_hour + (EEG_REM_min/60), digits = 3),
        "EEG_light_duration" = round(EEG_light_hour + (EEG_light_min/60), digits = 3),
        "EEG_wake_duration" = round(EEG_wake_hour + (EEG_wake_min/60), digits = 3),
        
        "oura_total_duration" = round(oura_sleep_duration_hour + (oura_sleep_duration_min/60), digits = 3),
        "oura_deep_duration" = round(oura_deep_hour + (oura_deep_min/60), digits = 3),
        "oura_REM_duration" = round(oura_REM_hour + (oura_REM_min/60), digits = 3),
        "oura_light_duration" = round(oura_light_hour + (oura_light_min/60), digits = 3),
        "oura_wake_duration" = round(oura_wake_hour + (oura_wake_min/60), digits = 3),
        
        "whoop_total_duration" = round(whoop_sleep_duration_hour + (whoop_sleep_duration_min/60), digits = 3),
        "whoop_deep_duration" = round(whoop_deep_hour + (whoop_deep_min/60), digits = 3),
        "whoop_REM_duration" = round(whoop_REM_hour + (whoop_REM_min/60), digits = 3),
        "whoop_light_duration" = round(whoop_light_hour + (whoop_light_min/60), digits = 3),
        "whoop_wake_duration" = round(whoop_wake_hour + (whoop_wake_min/60), digits = 3)
      )

    str(sleep_summarized)

    ## tibble [30 × 16] (S3: tbl_df/tbl/data.frame)
    ##  $ Date                : chr [1:30] "1/29/2023" "1/30/2023" "1/31/2023" "2/1/2023" ...
    ##  $ EEG_total_duration  : num [1:30] 6.98 6.37 5.48 7.48 9.08 ...
    ##  $ EEG_deep_duration   : num [1:30] 1.3 1.12 1.23 1.23 1.67 ...
    ##  $ EEG_REM_duration    : num [1:30] 1.867 2.117 0.633 2.683 3.3 ...
    ##  $ EEG_light_duration  : num [1:30] 3.88 3.2 3.72 3.75 4.18 ...
    ##  $ EEG_wake_duration   : num [1:30] 0.233 0.517 0.083 0.167 0.067 0.2 0.283 0.433 0.517 0.167 ...
    ##  $ oura_total_duration : num [1:30] 6.68 6.4 8.35 7.3 8.48 ...
    ##  $ oura_deep_duration  : num [1:30] 2.95 1.92 3.3 2.82 2.87 ...
    ##  $ oura_REM_duration   : num [1:30] 0.567 1.367 1.033 0.983 1.817 ...
    ##  $ oura_light_duration : num [1:30] 0.5 3.12 4.03 3.5 3.82 ...
    ##  $ oura_wake_duration  : num [1:30] 0.95 0.483 0.833 0.533 0.783 0.383 0.367 0.5 0.35 1.5 ...
    ##  $ whoop_total_duration: num [1:30] 6.67 6.28 7.83 7.08 8.4 ...
    ##  $ whoop_deep_duration : num [1:30] 1.42 1.12 1.43 1.67 2.42 ...
    ##  $ whoop_REM_duration  : num [1:30] 1.88 1.48 1.95 2.1 1.98 ...
    ##  $ whoop_light_duration: num [1:30] 3.37 3.68 4.45 3.32 4 ...
    ##  $ whoop_wake_duration : num [1:30] 0.517 0.567 1.083 0.733 0.75 ...

Now we have numeric columns with the true durations presented in hour
format with decimal representing minute / 60.

### Statistical Analysis to Compare to Criterion Measure

Now I would like to compare the durations of total, deep, REM and light sleep and duration awake for the Oura Ring and Whoop to the Dreem 2 EEG. In order to make these comparisons, I will use two techniques:  
- Pearson’s Correlation Coefficient  
- independent samples t-tests  
- absolute error  
- z-score absolute error  
- mean absolute percent error

For all of these analyses, the Dreem 2 EEG will be used as the reference value and the values collected from the Oura Ring and Whoop will be used to separately calculate the comparison measures. The differences will be visualized using Bland-Altman plots. Bland-Altman plots display the relationship between to values that are on the same scale. It presents the difference between the two measurements on the y-axis and the average of the two measurements on the x-axis. The values from each device were matched by date (see `group_by` in the code snippet).

The actual data analysis can be viewed by following [this link to my RShiny app](https://emalvat.shinyapps.io/COTS_wearables_sleep_comparison/)!!! Interpretations are presented as well.

Feel free to check out my Github if you would like to [download the dataset](https://github.com/emalyvatne/emalyvatne.github.io/blob/main/content/post/2023-11-24-wearable-sleep/sleep-data.csv) and explore for yourself!

### References

Arnal, P. J., Thorey, V., Debellemaniere, E., Ballard, M. E., Bou
Hernandez, A., Guillot, A., Jourde, H., Harris, M., Guillard, M., Van
Beers, P., Chennaoui, M., & Sauvet, F. (2020). The Dreem Headband
compared to polysomnography for electroencephalographic signal
acquisition and sleep staging. Sleep, 43(11), zsaa097.
<https://doi.org/10.1093/sleep/zsaa097>

Chinoy, E. D., Cuellar, J. A., Jameson, J. T., & Markwald, R. R. (2022).
Performance of Four Commercial Wearable Sleep-Tracking Devices Tested
Under Unrestricted Conditions at Home in Healthy Young Adults. Nature
and Science of Sleep, 14, 493–516. <https://doi.org/10.2147/NSS.S348795>

Craven, J., McCartney, D., Desbrow, B., Sabapathy, S., Bellinger, P.,
Roberts, L., & Irwin, C. (2022). Effects of Acute Sleep Loss on Physical
Performance: A Systematic and Meta-Analytical Review. Sports Medicine
(Auckland, N.Z.), 52(11), 2669–2690.
<https://doi.org/10.1007/s40279-022-01706-y>

della Monica, C., Johnsen, S., Atzori, G., Groeger, J. A., & Dijk, D.-J.
(2018). Rapid Eye Movement Sleep, Sleep Continuity and Slow Wave Sleep
as Predictors of Cognition, Mood, and Subjective Sleep Quality in
Healthy Men and Women, Aged 20–84 Years. Frontiers in Psychiatry, 9.
<https://www.frontiersin.org/articles/10.3389/fpsyt.2018.00255>

Driller, M. W., Dunican, I. C., Omond, S. E. T., Boukhris, O.,
Stevenson, S., Lambing, K., & Bender, A. M. (2023). Pyjamas,
Polysomnography and Professional Athletes: The Role of Sleep Tracking
Technology in Sport. Sports, 11(1), Article 1.
<https://doi.org/10.3390/sports11010014>

Halson, S. L., & Juliff, L. E. (2017). Chapter 2—Sleep, sport, and the
brain. In M. R. Wilson, V. Walsh, & B. Parkin (Eds.), Progress in Brain
Research (Vol. 234, pp. 13–31). Elsevier.
<https://doi.org/10.1016/bs.pbr.2017.06.006>

Lee, J.-M., Byun, W., Keill, A., Dinkel, D., & Seo, Y. (2018).
Comparison of Wearable Trackers’ Ability to Estimate Sleep.
International Journal of Environmental Research and Public Health,
15(6), Article 6. <https://doi.org/10.3390/ijerph15061265>

Mah, C. D., Kezirian, E. J., Marcello, B. M., & Dement, W. C. (2018).
Poor sleep quality and insufficient sleep of a collegiate
student-athlete population. Sleep Health, 4(3), 251–257.
<https://doi.org/10.1016/j.sleh.2018.02.005>

Merfeld, B., Mancosky, A., Luedke, J., Griesmer, S., Erickson, J. L.,
Carvalho, V., & Jagim, A. R. (2022). The Impact of Early Morning
Training Sessions on Total Sleep Time in Collegiate Athletes.

Miller, D. J., Sargent, C., & Roach, G. D. (2022). A Validation of Six
Wearable Devices for Estimating Sleep, Heart Rate and Heart Rate
Variability in Healthy Adults. Sensors (Basel, Switzerland), 22(16),
6317. <https://doi.org/10.3390/s22166317>

O’Donnell, S., Beaven, C. M., & Driller, M. W. (2018). From pillow to
podium: A review on understanding sleep for elite athletes. Nature and
Science of Sleep, 10, 243–253. <https://doi.org/10.2147/NSS.S158598>

Stone, J. D., Ulman, H. K., Tran, K., Thompson, A. G., Halter, M. D.,
Ramadan, J. H., Stephenson, M., Finomore, V. S., Galster, S. M., Rezai,
A. R., & Hagen, J. A. (2021). Assessing the Accuracy of Popular
Commercial Technologies That Measure Resting Heart Rate and Heart Rate
Variability. Frontiers in Sports and Active Living, 3, 585870.
<https://doi.org/10.3389/fspor.2021.585870>

Svensson, T., Chung, U., Tokuno, S., Nakamura, M., & Svensson, A. K.
(2019). A validation study of a consumer wearable sleep tracker compared
to a portable EEG system in naturalistic conditions. Journal of
Psychosomatic Research, 126, 109822.
<https://doi.org/10.1016/j.jpsychores.2019.109822>

Walsh, N. P., Halson, S. L., Sargent, C., Roach, G. D., Nédélec, M.,
Gupta, L., Leeder, J., Fullagar, H. H., Coutts, A. J., Edwards, B. J.,
Pullinger, S. A., Robertson, C. M., Burniston, J. G., Lastella, M.,
Meur, Y. L., Hausswirth, C., Bender, A. M., Grandner, M. A., & Samuels,
C. H. (2021). Sleep and the athlete: Narrative review and 2021 expert
consensus recommendations. British Journal of Sports Medicine, 55(7),
356–368. <https://doi.org/10.1136/bjsports-2020-102025>

Watson, A. M. (2017). Sleep and Athletic Performance. Current Sports
Medicine Reports, 16(6), 413.
<https://doi.org/10.1249/JSR.0000000000000418>
