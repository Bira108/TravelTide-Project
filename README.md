# TravelTide Rewards Program
### Customer Segmentation & Personalized Perk Assignment

**Tools:** PostgreSQL · Tableau Public · Beekeeper Studio · Google Sheets  
**Skills:** SQL · Feature Engineering · Data Visualization · Business Storytelling  
**Status:** Completed — DASep2025 Cohort Project

---

## What This Project Does

TravelTide is a travel booking platform with strong inventory but poor customer retention. 
This project designs a data-driven rewards program by segmenting high-engagement users 
through behavioral analysis and assigning each user one personalized perk — the one most 
likely to bring them back to the platform.

**The core question:** Instead of offering everyone the same discount, what if we gave 
each customer the one perk they actually want?

---

## Key Findings

- **46.9% of committed bookers never use discounts** — a blanket discount campaign 
  wastes nearly half its budget on customers who book at full price anyway
- **Married customers with children** consistently book more seats, stay longer and spend 
  more on hotels than any other demographic group — confirmed across two independent analyses
- **792 high-engagement users** never completed a booking despite repeated platform visits — 
  the highest-potential conversion target in the cohort
- **5,998 users** profiled and assigned one personalized perk each across 6 targeted segments

---

## Business Impact

| Opportunity | Conservative Projection |
|---|---|
| Converting 20% of non-booking users | $509,000 in incremental revenue |
| Retaining top 10% of high-CLV customers | $192,000 in protected annual revenue |
| Replacing blanket discounts with targeted perks | ~40% reduction in discount spend |

*Based on average customer lifetime value of $3,201 across the cohort.*

---

## Repository Structure

```
TravelTide-Project/
├── README.md
├── data/
│   └── TravelTide - Session Table - Cleaned.xlsx
├── sql/
│   └── TravelTide - Execution Plan (Report & Code).docx
├── tableau/
│   └── TravelTide Project - Final.twbx
└── reports/
    ├── TravelTide - Stakeholder Summary.docx
    ├── TravelTide - Technical Report.docx
    └── TravelTide Presentation - Slides.pptx
```

---

## Analytical Pipeline

### 1. Cohort Definition — PostgreSQL
Filtered 49,211 sessions to users with more than 7 sessions after January 4th 2023.  
Result: **5,998 high-engagement users**.  
Used LEFT JOINs to preserve non-booking sessions — enabling Non-Booker segment analysis.

> **Key decision:** LEFT JOIN instead of INNER JOIN. INNER JOIN would have made 
> the Non-Booker segment invisible by excluding sessions with no booking outcome.

---

### 2. Data Cleaning — Tableau Public
- Fixed date/time field types (session_start, departure_time, check_in, check_out)
- Assigned geographic roles to lat/lon coordinate fields
- Created `nights_clean` to exclude zero and negative night values
- Created `Booking Status` field classifying sessions as Full Booking, Flight Only, 
  Hotel Only or No Booking

---

### 3. Exploratory Analysis — Tableau Public (6 Charts)

| Chart | Type | Key Finding |
|---|---|---|
| Checked Bags | Histogram | Near 50/50 split — two distinct traveler commitment levels |
| Hotel Loyalists | Scatter Plot | Distinct cluster of high-price, long-stay customers |
| Deal Hunters | Bar Chart | 46.9% never use discounts — only 10.7% always do |
| Family Travelers | Diverging Bar | Married with children above average on all metrics simultaneously |
| Customer Value Map | Bubble Chart | Families cluster at higher spend — confirmed independently |
| Synthesis | Scatter Plot | Four customer value tiers combining spend, stay and engagement |

---

### 4. Feature Engineering — PostgreSQL (7 Metrics)

| Metric | Type | Method |
|---|---|---|
| `avg_lead_time_days` | Numeric | EXTRACT(EPOCH) from session_start to departure_time ÷ 86400 |
| `booking_completion_rate` | Numeric | Full bookings ÷ total sessions per user |
| `customer_lifetime_value` | Numeric | SUM(base_fare + hotel_price × rooms × nights) per user |
| `trip_length_category` | Segment | AVG(nights): Weekender ≤2, Standard ≤5, Extended >5 |
| `distance_category` | Segment | Haversine formula: Local <500km, Domestic <4000km, International 4000km+ |
| `discount_sensitivity` | Segment | Discount sessions ÷ full booking sessions per user |
| `family_segment` | Segment | Combination of married and has_children boolean fields |

> **Key decision:** Pre-aggregation CTE pattern — each table aggregated to user level 
> in a separate CTE before joining. Prevents row multiplication when joining 
> one-to-many tables.

---

### 5. Perk Assignment — PostgreSQL

One perk assigned per user using a priority CASE statement. Priority order reflects 
revenue impact and behavioral evidence from the Tableau analysis.

| Priority | Segment | Perk | Users |
|---|---|---|---|
| 1 | Married With Children | Free Seat Bundle | 1,084 |
| 2 | International Travelers | Free Checked Bag | 266 |
| 3 | Habitual Deal Hunters | Exclusive Discount | 2,221 |
| 4 | Non-Bookers | 1 Night Free Hotel With Flight | 792 |
| 5 | High CLV Customers (>$3,201) | No Cancellation Fees | 573 |
| 6 | All Remaining Users | Free Hotel Meal | 1,062 |

---

### 6. Campaign Visualizations — Tableau Public

- **Viz 1:** Perk Distribution Bar Chart — campaign scale per segment
- **Viz 2:** Perk by Family Segment Heatmap — confirms perk exclusivity (blank cells = no overlap by design)

---

## Key Analytical Decisions

| Decision | Rationale |
|---|---|
| LEFT JOIN over INNER JOIN | Preserve non-booking sessions for Non-Booker segment |
| Cohort: >7 sessions post Jan 2023 | Focus on users with demonstrated platform engagement |
| Two data sources in Tableau | Session table (49,211 rows) for behavior, metrics table (5,998 rows) for output |
| Haversine formula | Standard great circle distance — accurate for geographic coordinate pairs |
| Pre-aggregation CTE pattern | Prevents row multiplication in multi-table JOINs |
| Priority order in perk assignment | First matching condition wins — families before discount hunters |

---

## Limitations

- CAC could not be calculated — marketing spend data not available
- Distance threshold of 4,000km may classify some long US domestic routes as International
- Financial projections are conservative estimates based on available booking data
- Phased rollout timing and A/B testing design are outside the scope of this analysis

---

## Results Summary

| Output | Value |
|---|---|
| Users in cohort | 5,998 |
| Sessions analyzed | 49,211 |
| Behavioral metrics engineered | 7 |
| Customer segments identified | 6 |
| Perks assigned | 6 — one per user |
| Projected incremental revenue | $509,000 |
| Projected reduction in discount spend | ~40% |

---

## Deliverables

- 8 Tableau visualizations (6 exploratory + 2 campaign)
- Full SQL pipeline with CTEs, Haversine formula and perk assignment logic
- Stakeholder executive summary with financial projections
- Technical execution plan with methodology and learning notes
- 9-slide business presentation for Head of Marketing

---

*Ubiratan Gonzaga e Silva | DASep2025*
