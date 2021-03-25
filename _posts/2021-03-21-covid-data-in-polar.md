---
title: "A better way to represent death data in the era of COVID-19 🦠"
excerpt: "Found an interesting method to plot periodical data."
layout: default
math: false
categories: Data
---

I recently saw an interesting [video](https://www.youtube.com/watch?v=VTdVPNvwULM&t=301s) from Numberphile (great channel by the way) about _Nightingale diagrams_, which are particularly useful for plotting periodic data. 

In the video, Professor Mike Merrifield explains the history and usefulness of the _Nightingale Diagrams_, popularized by Florence Nightingale, who lived in 19th century England. I found this very interesting so I visited the website of the _Federal Statistical Office of Germany_ where you can download the current [data of deaths in Germany](https://www.destatis.de/DE/Themen/Gesellschaft-Umwelt/Bevoelkerung/Sterbefaelle-Lebenserwartung/Tabellen/sonderauswertung-sterbefaelle.html) from 2016 to mid-March 2021.

I plotted the data via `Python` and `matplotlib` and get the following:

{:refdef: style="text-align: center;"}
[![](/assets/images/blog/Death_Rate_Day.png){:height="75%" width="75%"}](/assets/images/blog/Death_Rate_Day.png)
{: refdef}

You read the chart clockwise, from January to December. The different colors indicate different years. It's quickly visible that the average deaths per day are between 2,500 and 3,000.

The orange line represents 2020, until the end of March we see a slight increase in deaths, during this period the first lockdown was announced. Around the middle of August there was another peak, this is probably due to the vacations here in Germany. In the colder months, from November to the end of 2020, there was a sharp increase due to the holidays and because people tend to stay indoors. Since then, the numbers have been declining, see blue line.

You may be wondering what happened between late February and late March 2018: Germany experienced a major flu epidemic, the deadliest in 30 years, which killed more than 25,000 people. In general, 2017/2018 saw one of the most severe influenza waves for Germany.

### Conlusion

With this kind of plots, the effect of COVID-19 is much more visible, Corona does not kill many people quickly, but evenly over time, but it also shows that even the flu can be serious for most people.

**So, remember to wash your hands 🧼, wear a mask 😷 and keep a distance from others!**