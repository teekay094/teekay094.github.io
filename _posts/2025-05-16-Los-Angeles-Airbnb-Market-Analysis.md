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
We marry exploratory data analysis (EDA) with policy & market research to explain price movements.
Key questions:
1. Which L.A. neighborhoods are most/least expensive?
2. How does capacity (beds) drive price in premium enclaves?
3. What happened to listings & rates when regulation was debated (2016-17) and when COVID-19 struck (2020)?
<br>

