---
layout:post
title: An analysis of Nobel Prize winners by country and category
subtitle : and which countries have punched above their weight

##Introduction

I found a very comprehensive [dataset](https://data.world/sya/nobel-prize-winners/workspace/file?filename=nobel_prize_by_winner.csv) from the Nobel Prize academy detailing all winners, birth, death and year of win.  In this post I will do some basic analysis on the data and I suppose further analysis could be done on whether educational systems are more important, or networks of people active at similar times

##Data Cleaning

There was a *huge* variety of data to be cleaned up: missing dates, missing countries stemming from many prizes being awarded to international organizations, archaic country names (i.e. USSR (now Belarus)) and joining first names and last names for a project which i won't display here (yet!).
Various methods were used including to_datetime, regex and try excepts to find the last stubborn strings which weren't complying.

```import pandas as pd
import numpy as np
nobel_prize = pd.read_csv('nobel_prize_by_winner.csv', encoding = "ISO-8859-1")```
---




