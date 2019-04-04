---
layout:post
title: An analysis of Nobel Prize winners by country and category
subtitle : and which countries have punched above their weight
---

### Introduction

I found a very comprehensive [dataset](http://data.nobelprize.org/) from the Nobel Prize academy detailing all winners, birth, death and year of win.  In this post I will do some basic analysis on the data and I suppose further analysis could be done on whether educational systems are more important, or networks of people active at similar times

### Data Cleaning

There was a decent variety of data to be cleaned up : missing dates, missing countries stemming from many prizes being awarded to international organizations, archaic country names (i.e. USSR (now Belarus)) and joining first names and last names for a project which I won't display here (yet!).
Various methods were used including to_datetime, regex and try excepts to find the last stubborn strings which weren't complying.

```import pandas as pd
import numpy as np
nobel_prize = pd.read_csv('nobel_prize_by_winner.csv', encoding = "ISO-8859-1")
#lots and lots of data cleaning - changing to date time - missing years - changing archaic country names etc etc
nobel_prize['died'] = nobel_prize['died'].str.replace('0000-00-00','4/2/2019')
nobel_prize = nobel_prize[nobel_prize['born'] != '0000/00/00']
nobel_prize['year'] = nobel_prize['year'].astype(int).astype(str)
nobel_prize.at[ 506,'born'] = '1/1/1943'
nobel_prize.at[ 945,'born'] = '1/1/1898'
nobel_prize['born'] = pd.to_datetime(nobel_prize['born'], infer_datetime_format=True)
nobel_prize['died'] = pd.to_datetime(nobel_prize['died'], infer_datetime_format=True)
nobel_prize['year'] = pd.to_datetime(nobel_prize['year'], format='%Y')
nobel_prize['surname'] = nobel_prize['surname'].fillna('')
nobel_prize['full_name'] =nobel_prize[['firstname', 'surname']].apply(lambda x: ' '.join(x), axis=1)
```

...and yet more cleaning...

```nobel_prize['bornCountry'] = nobel_prize['bornCountry'].replace(np.nan,'International Organization')
nobel_prize['bornCountryCode'] = nobel_prize['bornCountryCode'].replace(np.nan,'IO')
#making new column with clean country names
cleanlist = []
clist = list(nobel_prize['bornCountry']) 
for country in clist:
  aaa = re.sub(r'([\sA-Za-z0-9-]+\s\(\w+\s)([A-Za-z]+)',r'\2',country).strip(')')
  cleanlist.append(aaa)

nobel_prize['country of birth']=cleanlist
nobel_prize['country of birth'] = nobel_prize['country of birth'].str.replace('W&uuml;Germany','Germany')
nobel_prize['country of birth'] = nobel_prize['country of birth'].str.replace('Faroe Islands \(\Denmark','Denmark')
top_winners = list(nobel_prize['country of birth'].value_counts().head(5).index)
nobel_prize['year of win'] =nobel_prize['year'].dt.year
```
Then I made a cross tab of the data to make our first plot

```nobel_top = nobel_prize[nobel_prize['country of birth'].isin(top_winners)]
win_time_bin_top = pd.cut(nobel_top['year of win'], 10,precision=0) 
nobel_ct = pd.crosstab(nobel_top['country of birth'], win_time_bin_top).head()
nobel_ct
```

Here is the first plot I made

![stacked bar](/img/stacked bar.png?raw=true)
---




