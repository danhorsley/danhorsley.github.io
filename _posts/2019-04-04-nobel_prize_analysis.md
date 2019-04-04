---
layout: post
title: Nobel Laureate Analysis
image: /img/nobel2.jpeg
---
I found a very comprehensive [dataset](http://data.nobelprize.org/) from the Nobel Prize academy detailing all winners, birth, death and year of win.  In this post I will do some basic analysis on the data and I suppose further analysis could be done on whether educational systems are more important, or networks of people active at similar times

### Data Cleaning

There was a decent variety of data to be cleaned up : missing dates, missing countries stemming from many prizes being awarded to international organizations, archaic country names (i.e. USSR (now Belarus)) and joining first names and last names for a project which I won't display here (yet!).
Various methods were used including to_datetime, regex and try excepts to find the last stubborn strings which weren't complying.

```
import pandas as pd
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

```
nobel_prize['bornCountry'] = nobel_prize['bornCountry'].replace(np.nan,'International Organization')
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

```
nobel_top = nobel_prize[nobel_prize['country of birth'].isin(top_winners)]
win_time_bin_top = pd.cut(nobel_top['year of win'], 10,precision=0) 
nobel_ct = pd.crosstab(nobel_top['country of birth'], win_time_bin_top).head()
import seaborn as sns
sns.set()
ax = nobel_ct.T.plot(kind='bar', stacked=True, colormap = 'Spectral')
ax.figure.set_size_inches(15, 8)
ax.set(facecolor='#f9f9f9')
ax.set_title('Stacked Bar Chart - number of Nobel Prize winners by country and year of win');
```

...which produced this stacked bar chart...

![](/img/stacked%20bar.png)

### Now what?

I decided to look into which countries did well in which categories so i made a cross tab of country vs category and plotted it as a heat map

```
top_winners20 = list(nobel_prize['country of birth'].value_counts().head(20).index)
nobel_top20 = nobel_prize[nobel_prize['country of birth'].isin(top_winners20)]
ct_subject = pd.crosstab(nobel_top20['country of birth'], nobel_top20['category'])
ax_subject = sns.heatmap(ct_subject.T,cmap='Blues')
ax_subject.figure.set_size_inches(15, 8)
```

and the result was ...

![](/img/nobel%20heatmap%201.png)

### Misleading?

It's easy to see how countries with larger populations would dominate, but how about we adjust contribution per million of population to see who is the best country pound for pound by category?

First we had to import some population data

```
df_pop = pd.read_csv('https://raw.githubusercontent.com/datasets/population/master/data/population.csv')

df_pop = df_pop.rename(columns = {'Country Name':'country of birth'})
df_pop = df_pop.set_index('country of birth')
pop_average = df_pop.groupby(level=0).mean()
pop_average = pop_average.drop(['Year'], axis=1)
pop_average.index = pop_average.index.str.replace('United States','USA')
pop_average.index = pop_average.index.str.replace('Netherlands','the Netherlands')
```
then we merged it into existing dataframe and divided the winners by population average and multiplying by 1 million...

```
merged = pd.merge(ct_subject, pop_average, left_index=True, right_index=True, how='inner')
subject_adjusted_for_pop = merged.div(merged.Value,axis=0)*1000000
subject_adjusted_for_pop = subject_adjusted_for_pop.drop(['Value'], axis=1)
```

before doing one last plot

```
ax_subject_adj = sns.heatmap(subject_adjusted_for_pop.T,cmap='Blues')
ax_subject_adj.figure.set_size_inches(15, 8)
ax_subject_adj.set_title('Nobel Prize Winners by category per million population')
```

![](/img/nobel%20heat%20map%202.png)

### Conclusions

We can see that pound for pound Austria is the chemistry boss (has this got some link with industry?), Norway leads in economics, Sweden and Denmark seem to produce good novels (bias here from the academy as I never read any of them!?!).  Austria, Switzerland and Denmark are helping us live longer, peace is more evenly distributed (which makes sense by the very nature of problems occuring randomly all over the world) as is physics which also makes sense as there would be the least amount of bias in the decision making process (especially versus literature).

The next goal for this project would be to analyse networks between the winners and various institutions. 

---




