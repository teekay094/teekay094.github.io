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
- [06. Insights & Discussion](#discussion)

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
