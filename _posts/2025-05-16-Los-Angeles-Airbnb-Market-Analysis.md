---
layout: post
title: Los Angeles AirBnB Marketing Analysis
image: "los_angeles.jpg"
tags: [AirBnB, Exploratory Data Analysis, Python]
---

I have been a Los Angeles native for about 5 years now, everytime a loved one visits it is obligatory for me to show them around. This whole project came in to existence because earlier this year a friend of mine was visiting Los Angeles and they asked for some help in finding a reasonable AirBnB accomodation. I started looking up AirBnB Listings and the variance in the pricing was very fascinating. After helping them find a really sweet spot in West LA I though about doing a deep dive in to the AirBnB Listings & Market specifically in Los Angeles. From coastal mansions to cozy East-Side bungalows, nightly prices in L.A. differ six-fold. As I kept digging in to the data and looking at trends I realized that the rental regulations in 2016-17 and COVID-19 had great effect on the AirBnB Market in Los Angeles.

# Table of contents

- [00. Project Overview](#overview-main)
    - [Context](#overview-context)
    - [Actions](#overview-actions)
    - [Results & Discussion](#overview-results)
- [01. Concept Overview](#concept-overview)
- [02. Data Overview & Preparation](#data-overview)
- [03. Exploratory Analysis](#exploratory-analysis)
- [04. Regulatory Deep Dive (2016-2017)](#regulatory-deep-dive-analysis)
- [05. COVID-19 Impact Analysis](#covid-19-impact-analysis)
- [06. Key Insights & Take-aways](#key-insights)
- [07. Full Reproducible Code](#full-code) 

___

# Project Overview  <a name="overview-main"></a>

### Context <a name="overview-context"></a>

Los Angeles began crafting short-term-rental (STR) rules in April 2016 with a draft Home-Sharing Ordinance. Public debate stretched through 2017, culminating in the final ordinance adopted in December 2018. When COVID-19 hit in early 2020, global travel collapsed—yet demand later rebounded on the back of long-stay “work-from-anywhere” trips.

<br>
<br>
### Actions <a name="overview-actions"></a>
We pulled the latest AirBnb Data, cleaned missing values, and built visuals to show:

* Correlations among numeric fields.
* Mean price by neighborhood & capacity.
* Year-on-year host growth and average nightly rates.

<br>
<br>
### Results & Discussion <a name="overview-results"></a>

* Bel-Air, Venice, Malibu command the highest nightly rates, while Lincoln Heights, Pacoima, Watts sit at the bottom range.
* Listing growth stalled right after the 2016 draft law surfaced, even before enforcement began. 
* Average nightly price dipped ≈15 % in 2020, but exceeded 2019 levels by mid-2021 as bookings shifted to 28-day-plus stays.

<br>
<br>
___
# Concept Overview  <a name="concept-overview"></a>

<br>
Los Angeles offers an ideal natural experiment for studying how **local policy shocks** and a **global demand shock** interact in a large, high-value short-term-rental (STR) market.  
This project weaves together three analytical strands:

| Strand | What we do | Why it matters |
|--------|------------|----------------|
| **Descriptive market mapping** | • Clean the latest *InsideAirbnb* snapshot<br>• Profile host tenure, occupancy capacity, review counts, and price distribution city-wide | Establishes the baseline “shape” of supply and price dispersion before layering in external forces |
| **Spatial segmentation** | • Aggregate listings by `neighbourhood_cleansed` and rank mean nightly rates<br>• Visualize the top- and bottom-priced tracts, then drill into capacity-price curves for luxury enclaves like Bel-Air | Shows how geography and property size magnify price gaps—critical for housing-affordability debates |
| **Temporal & event-study analysis** | • Resample hosts and prices by `host_since` year<br>• Overlay two exogenous events: (1) the 2016–2017 Home-Sharing Ordinance debate, and (2) the 2020 COVID-19 travel collapse | Quantifies how *regulatory expectations* (not just final laws) slow supply growth, and how a sudden demand shock first depresses, then re-prices the market |

### Research questions we answer

1. **Price ladder:** How steep is the nightly price gradient across L.A. neighborhoods, and which factors—location or capacity—drive it most?  
2. **Policy anticipatory effects:** Did listing growth decelerate as soon as the draft ordinance appeared, even before enforcement?  
3. **Pandemic resilience:** How quickly did nightly rates and active listings recover after the initial 2020 plunge, and did hosts pivot toward long-stay bookings?  

### Methodological touchpoints

* **Data engineering** – Robust missing-value imputation (median for numerics, mode for categoricals) to ensure fair cross-neighborhood comparisons.  
* **Visualization-first EDA** – Correlation heat-maps, dual-axis time series, and wrapped bar-charts make complex patterns legible at a glance.  
* **Policy literature triangulation** – Pair findings with academic papers, city-council dockets, and AirDNA trend reports to ground numbers in real-world context.  

By integrating *granular listing data* with *policy chronology* and *pandemic timelines*, we produce insights that inform hosts weighing investment decisions, city officials balancing tourism with housing pressures, and travelers hunting value in a post-COVID landscape.
<br>
___
<br>
# Data Overview & Preparation  <a name="data-overview"></a>

For this project we rely on the public **InsideAirbnb “listings” snapshot** for Los Angeles.  
Our immediate goal is to clean the raw file just enough to support the exploratory work that follows.

In the code below, we:

* Load the Python libraries needed for data wrangling and visualization  
* Import the *listings.csv* snapshot and parse the `host_since` date column  
* Impute missing values — **median** for all numeric columns  and **mode** for all categorical columns — to avoid bias from row-wise deletion  
* Trim the dataset down to the four fields required for the rest of the analysis:  
  `host_since`, `neighbourhood_cleansed`, `accommodates`, and `price`

<br>

```python
# --- Quick Summary --------------------------------------------
# • Load snapshot and parse host_since dates.  
# • Impute missing values (median for numerics).  
# • Keep only the columns we need for analysis.
# --------------------------------------------------------------

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

listings = pd.read_csv('listings.csv')

pds = listings.head()

listings.info()

listings['host_since'] = pd.to_datetime(listings['host_since'])

listings.info()

# Fill missing numerical values with median
numerical_cols = listings.select_dtypes(include=[np.number]).columns
listings[numerical_cols] = listings[numerical_cols].fillna(listings[numerical_cols].median())

# Fill missing categorical values with mode
categorical_cols = listings.select_dtypes(include=[object]).columns
listings[categorical_cols] = listings[categorical_cols].fillna(listings[categorical_cols].mode().iloc[0])

#Lis
losangeles_neighborhood = (listings.loc[:,['host_since','neighbourhood_cleansed','accommodates','price']])
losangeles_neighborhood.isna().sum()
losangeles_neighborhood.dropna(how='any',inplace = True)

losangeles_neighborhood.describe()

losangeles_neighborhood.query('price <= 10')

```

<br>
# Exploratory Analysis <a name="exploratory-analysis"></a>

### Correlation Heat-Map

```python
numeric_df = listings.select_dtypes(include=[np.number])
plt.figure(figsize=(12, 8))
sns.heatmap(numeric_df.corr(), annot=True, fmt='.2f', cmap='coolwarm')
plt.title('Correlation Heatmap')
plt.show()

```

<br>

![alt text](/img/posts/Correlation_Matrix.jpg)

## Quick Take-aways from the Heat-Map

| Observation | What it suggests |
|-------------|------------------|
| **Strong positive cluster:** `accommodates`, `bedrooms`, `bathrooms`, `beds` (ρ ≈ 0.62–0.81) | Bigger properties naturally come with more rooms and beds — no surprise, but the tight block of warm colors confirms multicollinearity among size variables. |
| **Price correlations:** highest with `bathrooms` (≈ 0.47) and `bedrooms` (≈ 0.36); moderate with `accommodates` (≈ 0.33) and `beds` (≈ 0.29) | Nightly price rises with property size, and bathrooms seem to be the single best numeric proxy for “luxury” in this dataset. |
| **Host & review metrics:** near-zero correlation between `host_response_rate` and `price`; slight negative tie between `number_of_reviews` and `price` | High responsiveness doesn’t translate into higher prices, and listings with lots of reviews tend to be mid-range or budget offerings. |
| **Temporal hint:** `id` (proxy for listing age) shows a **–0.32** correlation with `number_of_reviews` | Older listings (low `id` numbers) have had more time to accumulate reviews, matching intuition. |
| **Minimum nights & availability:** weak relationships with everything ( \|ρ\| < 0.15) | Minimum-stay rules and calendar availability don’t meaningfully co-vary with price in this snapshot. |

**Take-home:** Property *size* drives price far more than host behavior or review volume. Because the size variables move together, downstream modeling should avoid including them all in an **unregularized** regression—you’ll get inflated variance.

### Neighborhood Level Pricing

We plot the 10 most- and least-expensive neighborhoods, wrapping y-labels for readability:

```python
top_10    = losangeles_neighborhoods.nlargest(10, "price")
bottom_10 = losangeles_neighborhoods.nsmallest(10, "price")

import textwrap

def wrap_yticklabels(ax, width=20, align="right"):
    new_labels = [
        textwrap.fill(t.get_text(), width) for t in ax.get_yticklabels()
    ]
    ax.set_yticklabels(new_labels, ha=align)

def plot_neigh_prices(df, title, color):
    fig, ax = plt.subplots(figsize=(11, 6))

    (df.sort_values("price")
       .plot(kind="barh",
             y="price",
             ax=ax,
             legend=False,
             color=color,
             zorder=3))

    ax.set_title(title)
    ax.set_xlabel("Price per night (USD)")
    ax.set_ylabel("Neighborhood")
    ax.grid(axis="x", linestyle="--", alpha=0.4)

    wrap_yticklabels(ax, width=20)

    plt.tight_layout()
    return fig, ax

plot_neigh_prices(
    top_10,
    "HIGHEST AVERAGE LISTING PRICE BY LOS ANGELES NEIGHBORHOODS",
    color="tab:blue",
)

plot_neigh_prices(
    bottom_10,
    "LOWEST AVERAGE LISTING PRICE BY LOS ANGELES NEIGHBORHOODS",
    color="tab:green",
)

plt.show()

```

<br>

![alt text](/img/posts/highest_average_pricing_by_los_angeles_neighborhood.jpg)

![alt text](/img/posts/lowest_average_pricing_by_los_angeles_neighborhood.jpg)

*Bel-Air*, *Venice*, and *Malibu* remain the priciest, while *Lincoln Heights* and *Pacoima cluster* at the lower end.

## Accomodation Pricing

Using this data, I also looked in to the Average Listing Price by Accomodation Number in Los Angeles Neighborhoods

```python
import seaborn as sns

(losangeles_listing_accommodates.
     plot.
     barh(
         title = 'AVERAGE LISTING PRICE BY ACCOMMODATION NUMBER IN LOS ANGELES NEIGHBORHOODS',
         xlabel = 'Price per night(USD)',
         ylabel = 'Neighborhood',
         legend = None,
         color='purple'))

sns.despine()
plt.show()

```

<br>

![alt text](/img/posts/average_listing_price_accomodation_number.jpg)

## TEMPORAL TRENDS

First I started looking at Average Yearly price for Los Angeles Neighborhoods:

```python
losangeles_listing_overtime['price'].plot(
        ylabel = 'Average Price per night(USD)',
        title = 'AVERAGE LISTING PRICE OVER TIME IN LOS ANGELES NEIGHBORHOODS',
        color ='orange'
        )
sns.despine()
plt.show()

```
<br>

![alt text](/img/posts/average_listing_price_over_time.jpg)

Looking at this visual, I noticed that there were 2 phases over the years where price changed spike and I looked up in to more information and found that there was impact because of the 2016-17 rule-making period already created regulatory uncertainty and COVID-19. This intrigued me to do more research and take a deeper dive into regulations of Los Angeles and also the impact of COVID-19 on the number of listings & pricing.

Then I generated a Dual-Axis chart of AirBnB annual listing counts over the Year alongside the Average Pricing per Night in Los Angeles Neighborhoods, this allowed me to drive more insights about the Annual Listings and Average Pricing and the assumptions I had related to fluctuation in pricing near 2016-2017 and around 2019.

```python
df_yearly = losangeles_listing_overtime.copy()
df_yearly.index = df_yearly.index.year

fig, ax1 = plt.subplots(figsize=(10, 6))


ax1.bar(df_yearly.index,
        df_yearly['neighbourhood_cleansed'],
        color='tab:blue',
        label='Listing count')

ax1.set_xlabel('Year host_since')
ax1.set_ylabel('Number of listings', color='tab:blue')
ax1.tick_params(axis='y', labelcolor='tab:blue')

ax2 = ax1.twinx()
ax2.plot(df_yearly.index,
         df_yearly['price'],
         color='tab:orange',
         marker='o',
         linewidth=2,
         label='Average price')

ax2.set_ylabel('Average price (USD)', color='tab:orange')
ax2.tick_params(axis='y', labelcolor='tab:orange')

desired_ticks = [2007, 2010, 2012, 2014, 2016, 2018, 2020, 2022, 2024]
ax1.set_xticks(desired_ticks)
ax1.set_xticklabels(desired_ticks, rotation=45)

tick_labels = [t for t in desired_ticks if t in df_yearly.index]
ax1.set_xticks(tick_labels)
ax1.set_xticklabels(tick_labels, rotation=45)

handles1, labels1 = ax1.get_legend_handles_labels()
handles2, labels2 = ax2.get_legend_handles_labels()
ax1.legend(handles1 + handles2,
           labels1  + labels2,
           loc='upper left')

plt.title("YEARLY AIRBNB LISTING COUNT AND AVERAGE PRICE IN LOS ANGELES")
plt.tight_layout()
sns.despine()
plt.show()
```
<br>

![alt text](/img/posts/yearly_listing_count_airbnb.jpg)

## Observations

* Explosive host growth from 2014–2016, plateauing once the draft ordinance surfaced in 2016–17.
* A sharp price dip in 2020 coincides with the COVID-19 travel shutdown, followed by an above-trend rebound in 2021–22.

# Regulatory Context (2016-2017)  <a name="regulatory-deep-dive-analysis"></a>


| Milestone | Summary | Source |
|-----------|---------|--------|
| **Apr 2016 – Draft Home-Sharing Ordinance released** | First city document proposing to legalize STRs in primary residences, cap them at 90 days / yr, and create a host-registration system. | *Los Angeles City Planning* |
| **Jun 2016 – City Planning Commission forwards revised draft** | Ordinance advanced to City Council for debate. | *Los Angeles City Planning* |
| **Dec 2016 – Housing Committee amendments** | Added 180-day limit, clarified accessory-use status, and earmarked TOT revenue for enforcement. | *City of Los Angeles Clerk’s Office* |
| **Jan – Oct 2017 – Public comment & committee hearings** | Multiple neighborhood councils weighed in; the PLUM Committee scheduled deliberations in Oct 2017. | *City of Los Angeles Clerk’s Office* |

> *Although the final ordinance was not adopted until Dec 2018, the 2016-17 rule-making period already created regulatory uncertainty. Listings growth slowed and hosts began adjusting supply expectations, echoing quasi-experimental research on STR bans in 18 L.A.–county cities that found ≈ 50 % listing reductions post-regulation (ScienceDirect).*  

---

# COVID-19 Impact Analysis (2020-2021)  <a name="covid-19-impact-analysis"></a>

| Impact Point | Evidence | Source |
|--------------|----------|--------|
| **Occupancy & Supply Shock** | Nationwide 32 % drop in urban STR demand by Apr 2020; Los Angeles mirrored the decline. | *AirDNA* |
| **Listing Retrenchment** | Case studies across six global cities show 30-50 % of hosts delisted during Mar 2020. | *ScienceDirect* |
| **Price Dynamics** | UCLA thesis: avg. L.A. nightly rates fell ≈ 15 % YoY in 2020, rebounded above 2019 by mid-2021 via longer “work-from-anywhere” stays. | *eScholarship* |
| **Strategic Pivot** | Analysts predicted Airbnb would refocus on “live-like-a-local” & emphasize cleanliness to rebuild trust. | *Condé Nast Traveler* |
| **Host Resilience Tactics** | Hosts shifted to 28-plus-day stays and moved inventory to drive-to rural markets. | *UQ Pressbooks* / *PR Newswire* |

---

# Key Insights & Take-aways  <a name="key-insights"></a>

* **Neighborhood matters** – Median nightly rates span a six-fold range; coastal & hillside enclaves command persistent premiums.  
* **Capacity premium** – In high-end areas like *Bel-Air*, price scales super-linearly with `accommodates`; luxury villas skew averages upward.  
* **Regulation shapes supply** – The 2016 draft ordinance cooled listing growth even **before** adoption, supporting evidence that expectations alone influence host behavior (*ScienceDirect*).  
* **Pandemic reset** – COVID-19 compressed prices but also flushed out lightly committed hosts, leaving a leaner, more professionalized inventory by 2022.  
* **Policy-market feedback** – Post-COVID recovery plus full ordinance enforcement from 2019 onward suggest L.A. is settling into a new, lower-growth equilibrium balancing tourism dollars with housing concerns (*Los Angeles Housing Department*).

---

# Full Reproducible Code  <a name="full-code"></a>

Here's the full code:
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

listings = pd.read_csv('listings.csv')

pds = listings.head()

listings.info()

listings['host_since'] = pd.to_datetime(listings['host_since'])

listings.info()

# Fill missing numerical values with median
numerical_cols = listings.select_dtypes(include=[np.number]).columns
listings[numerical_cols] = listings[numerical_cols].fillna(listings[numerical_cols].median())

# Fill missing categorical values with mode
categorical_cols = listings.select_dtypes(include=[object]).columns
listings[categorical_cols] = listings[categorical_cols].fillna(listings[categorical_cols].mode().iloc[0])

# Correlation heatmap
numeric_df = listings.select_dtypes(include=[np.number])
plt.figure(figsize=(12, 8))
sns.heatmap(numeric_df.corr(), annot=True, fmt='.2f', cmap='coolwarm')
plt.title('Correlation Heatmap')
plt.show()

losangeles_neighborhood = (listings.loc[:,['host_since','neighbourhood_cleansed','accommodates','price']])

losangeles_neighborhood.isna().sum()

losangeles_neighborhood.dropna(how='any',inplace = True)

losangeles_neighborhood.describe()

losangeles_neighborhood.query('price <= 10')

losangeles_neighborhoods = (losangeles_neighborhood.groupby('neighbourhood_cleansed').agg({'price':'mean'}))
losangeles_neighborhoods.head()
losangeles_neighborhoods.sort_values('price',inplace=True)
losangeles_neighborhoods.tail()
                            
losangeles_listing_accommodates = (losangeles_neighborhood.query("neighbourhood_cleansed == 'Bel-Air'").groupby('accommodates').agg({'price':'mean'}).sort_values('price'))
losangeles_listing_accommodates.tail()

losangeles_listing_overtime  = (losangeles_neighborhood.set_index('host_since').resample("Y").agg({"neighbourhood_cleansed":"count","price":"mean"}))
losangeles_listing_overtime
    
#VISUALIZING THE DATA

## 1 - VISUALS FOR Yearly Airbnb Listing Count and Average Price in Los Angeles

df_yearly = losangeles_listing_overtime.copy()
df_yearly.index = df_yearly.index.year

fig, ax1 = plt.subplots(figsize=(10, 6))


ax1.bar(df_yearly.index,
        df_yearly['neighbourhood_cleansed'],
        color='tab:blue',
        label='Listing count')

ax1.set_xlabel('Year host_since')
ax1.set_ylabel('Number of listings', color='tab:blue')
ax1.tick_params(axis='y', labelcolor='tab:blue')

ax2 = ax1.twinx()
ax2.plot(df_yearly.index,
         df_yearly['price'],
         color='tab:orange',
         marker='o',
         linewidth=2,
         label='Average price')

ax2.set_ylabel('Average price (USD)', color='tab:orange')
ax2.tick_params(axis='y', labelcolor='tab:orange')

desired_ticks = [2007, 2010, 2012, 2014, 2016, 2018, 2020, 2022, 2024]
ax1.set_xticks(desired_ticks)
ax1.set_xticklabels(desired_ticks, rotation=45)

tick_labels = [t for t in desired_ticks if t in df_yearly.index]
ax1.set_xticks(tick_labels)
ax1.set_xticklabels(tick_labels, rotation=45)

handles1, labels1 = ax1.get_legend_handles_labels()
handles2, labels2 = ax2.get_legend_handles_labels()
ax1.legend(handles1 + handles2,
           labels1  + labels2,
           loc='upper left')

plt.title("YEARLY AIRBNB LISTING COUNT AND AVERAGE PRICE IN LOS ANGELES")
plt.tight_layout()
sns.despine()
plt.show()


## 2 - VISUALS FOR HIGHEST & LOWEST AVERAGE LISTING PRICE BY LOS ANGELES NEIGHBORHOODS
top_10    = losangeles_neighborhoods.nlargest(10, "price")
bottom_10 = losangeles_neighborhoods.nsmallest(10, "price")

import textwrap

def wrap_yticklabels(ax, width=20, align="right"):
    new_labels = [
        textwrap.fill(t.get_text(), width) for t in ax.get_yticklabels()
    ]
    ax.set_yticklabels(new_labels, ha=align)

def plot_neigh_prices(df, title, color):
    fig, ax = plt.subplots(figsize=(11, 6))

    (df.sort_values("price")
       .plot(kind="barh",
             y="price",
             ax=ax,
             legend=False,
             color=color,
             zorder=3))

    ax.set_title(title)
    ax.set_xlabel("Price per night (USD)")
    ax.set_ylabel("Neighborhood")
    ax.grid(axis="x", linestyle="--", alpha=0.4)

    wrap_yticklabels(ax, width=20)

    plt.tight_layout()
    return fig, ax

plot_neigh_prices(
    top_10,
    "HIGHEST AVERAGE LISTING PRICE BY LOS ANGELES NEIGHBORHOODS",
    color="tab:blue",
)

plot_neigh_prices(
    bottom_10,
    "LOWEST AVERAGE LISTING PRICE BY LOS ANGELES NEIGHBORHOODS",
    color="tab:green",
)

plt.show()

## VISUALS FOR PRICING PER ACCOMODATES FOR LOS ANGELES NEIGHBORHOODS

import seaborn as sns

(losangeles_listing_accommodates.
     plot.
     barh(
         title = 'AVERAGE LISTING PRICE BY ACCOMMODATION NUMBER IN LOS ANGELES NEIGHBORHOODS',
         xlabel = 'Price per night(USD)',
         ylabel = 'Neighborhood',
         legend = None,
         color='purple'))

sns.despine()
plt.show()

## 3 - VISUALS FOR NEW AIRBNB HOSTS FOR LOS ANGELES NEIGHBORHOODS

losangeles_listing_overtime['neighbourhood_cleansed'].plot(
        ylabel = 'New Hosts',
        title = 'NEW AIRBNB HOSTS OVER TIME IN LOS ANGELES NEIGHBORHOODS',
        color ='blue'
        )
sns.despine()
plt.show()

## 4 - VISUALS FOR AVERAGE PRICE PER LISTINGS FOR LOS ANGELES NEIGHBORHOODS
 
losangeles_listing_overtime['price'].plot(
        ylabel = 'Average Price per night(USD)',
        title = 'AVERAGE LISTING PRICE OVER TIME IN LOS ANGELES NEIGHBORHOODS',
        color ='orange'
        )
sns.despine()
plt.show()

## 5 - VISUALS FOR AVERAGE PRICE PER LISTINGS FOR LOS ANGELES NEIGHBORHOODS - COVID-19

ax = (losangeles_listing_overtime['price']
        .plot(
            ylabel='Average price per night (USD)',
            title='AVERAGE LISTING PRICE OVER TIME IN LOS ANGELES NEIGHBORHOODS',
            color='orange')
      )

year_marker = '2020'                     
y_val = losangeles_listing_overtime.loc[year_marker, 'price']

ax.scatter(pd.Timestamp(year_marker), y_val, s=80,  # circle size
           facecolor='none', edgecolor='red', zorder=5)

ax.annotate('COVID-19',
            xy=(pd.Timestamp(year_marker), y_val),
            xytext=(15, 25), textcoords='offset points',
            arrowprops=dict(arrowstyle='->', lw=1.2, color='red'),
            fontsize=9, color='red')

sns.despine()
plt.tight_layout()
plt.show()
```
<br>

Feel free to fork the notebook, plug in the latest InsideAirbnb snapshot, and explore further questions such as hedonic regression or synthetic-control comparisons with unregulated cities.

References
1. Draft Home-Sharing Ordinance, City of Los Angeles, Apr 15 2016 - Los Angeles City Planning
2. PLUM Committee public docket, Council File 14-1635-S2, Oct 2017 - City of Los Angeles Clerk's Office
3. City Council adopts Home-Sharing Ordinance, Dec 2018 - Los Angeles Housing Department
4. Quasi-experimental STR ban study, Regional Science & Urban Economics 2021 - ScienceDirect
5. Skift: Bookings down 85 % in April 2020 - Skift
6. Skift: Long-term stays resilient in 2020 - Skift
7. Airbnb Newsroom: Rise of remote workers, Sept 2022 - Airbnb Newsroom
8. InsideAirbnb price progression 2019-23 via HomeISD stats - HomeISD
9. AirDNA Los Angeles market overview, 2025
