# TravelTide Rewards Program 

## 1. Project Objective

Design and deliver a data-driven personalized rewards program for TravelTide. The objective was to segment high-engagement users by behavioral profile and assign each user one targeted perk most likely to bring them back to the platform.


## 2. Data Sources

Four tables from TravelTide's PostgreSQL database:


| Table |	Description |	Key Fields |
|------ | ----------- | ---------- |
| Sessions |	One row per browsing session |	session_id, user_id, session_start, flight_booked, hotel_booked, discounts, page_clicks |
| Users |	Customer demographic profile |	user_id, gender, married, has_children, home_airport, home coordinates, sign_up_date |
| Flights |	Trip flight details	| trip_id, origin, destination, seats, departure_time, base_fare_usd, coordinates |
| Hotels |	Trip hotel details	| trip_id, hotel_name, nights, rooms, check_in, hotel_price_per_room_night |


## 3. Tools

PostgreSQL (Beekeeper Studio)	Data extraction, cohort definition, feature engineering, perk assignment
Tableau Desktop	Exploratory analysis, behavioral segmentation visualization, campaign output charts
Microsoft Word / Google Docs	Execution plan and stakeholder summary documentation


## 4. Analytical Pipeline

**Step 1:** Cohort Definition
**Tool:** PostgreSQL / Beekeeper

Filtered the sessions table to users with more than 7 sessions after January 4th 2023. This produced a cohort of 5,998 high-engagement users from 49,211 total sessions. LEFT JOINs were used to include sessions where no booking occurred, enabling non-booker analysis. 

**Note:** LEFT JOIN vs INNER JOIN. INNER JOIN would have excluded non-booking sessions and made the Non-Booker segment invisible. LEFT JOIN preserves the full customer journey.


**Step 2:** Data Cleaning
**Tool:** Tableau Desktop

•	Fixed date and time field types (session_start, departure_time, check_in, check_out, birthdate)
•	Assigned geographic roles to lat/lon coordinate fields
•	Created nights_clean calculated field to exclude zero and negative night values
•	Created Booking Status field to classify sessions by outcome (Full Booking, Flight Only, Hotel Only, No Booking)
•	Identified and filtered 50 null values in synthesis chart (<1% of full booking sessions)

**Note:** Fixed data types in the Tableau Data Source tab before building any charts. CSV files have no enforced types, and Tableau auto-detects and sometimes gets it wrong (it did).


**Step 3:** Exploratory Analysis
**Tool:** Tableau Desktop

## Six charts built on the session-level table (49,211 rows):

Chart	Type	Key Finding
1 - Checked Bags	Histogram	Near 50/50 split between zero-bag and one-bag travelers
2 - Hotel Loyalists	Scatter Plot	Distinct cluster of high-price, long-stay customers
3 - Deal Hunters	Bar Chart	Habitual discount users form a distinct behavioral segment
4 - Family Travelers	Diverging Bar	Married with children above average on all three metrics simultaneously
5 - Customer Value Map	Bubble Chart	Families cluster at higher spend — confirmed across two analyses
6 - Synthesis	Scatter Plot	Four customer value tiers visible combining spend, stay and engagement


## Step 4: Feature Engineering
## Tool: PostgreSQL / Beekeeper

Seven behavioral metrics calculated per user using CTEs to avoid row multiplication from multi-table JOINs:

Metric	Type	Formula Basis
avg_lead_time_days	Numeric	EXTRACT(EPOCH) difference between session_start and departure_time / 86400
booking_completion_rate	Numeric	Full booking sessions / total sessions per user
customer_lifetime_value	Numeric	SUM of base_fare_usd + (hotel_price x rooms x nights) per user
trip_length_category	Segment	AVG(nights) bucketed: Weekender <=2, Standard <=5, Extended >5
distance_category	Segment	Haversine formula on lat/lon coordinates: Local <500km, Domestic <4000km, International 4000km+
discount_sensitivity	Segment	Discount sessions / full booking sessions: Non-Booker, Never, Occasional, Habitual
family_segment	Segment	Combination of married and has_children boolean fields

## Note: Pre-aggregation pattern: each table (sessions, flights, hotels) was aggregated to user level in a separate CTE before joining. This prevents row multiplication — a common SQL error when joining one-to-many tables.


## Step 5: Perk Assignment
## Tool: PostgreSQL / Beekeeper

A final CASE statement in the SELECT used segment labels from the user_segments CTE to assign one perk per user. Priority order was determined by revenue impact and analytical evidence from the Tableau charts.


Priority	Condition	Assigned Perk	Users
1	family_segment = Married With Children	Free Seat Bundle	1,084
2	distance_category = International	Free Checked Bag	266
3	discount_sensitivity = Habitual Deal Hunter	Exclusive Discount	2,221
4	discount_sensitivity = Non-Booker	1 Night Free Hotel With Flight	792
5	customer_lifetime_value > $3,201 (avg)	No Cancellation Fees	573
6	All remaining users	Free Hotel Meal	1,062


## Step 6: Campaign Visualizations
## Tool: Tableau Desktop


Two final visualizations built on the metrics table (5,998 rows — one row per user):

•	Viz 1: Perk Distribution Bar Chart — campaign scale per perk, sorted descending
•	Viz 2: Perk by Family Segment Heatmap — confirms perk exclusivity, blank cells = no overlap


# 5. Key Analytical Decisions


Decision	Reason
LEFT JOIN instead of INNER JOIN	Preserve non-booking sessions to enable Non-Booker segment analysis
Cohort: >7 sessions post Jan 2023	Focus perk investment on users with demonstrated platform engagement
Two data sources in Tableau	Session table for behavioral analysis (49,211 rows), metrics table for perk output (5,998 rows)
Haversine formula for distance	Standard great circle calculation — accurate for geographic coordinate pairs
Pre-aggregation CTE pattern	Prevents row multiplication when joining sessions, flights and hotels simultaneously
Priority order in perk assignment	First matching condition wins — families protected before discount hunters, maximizing revenue impact


# 6. Limitations and Assumptions

•	Customer Acquisition Cost (CAC) could not be calculated — marketing spend data not available in dataset.
•	Distance threshold of 4,000km may classify some long US domestic routes as International.
•	Financial projections (CLV $3,201 average) are conservative estimates based on available booking data.
•	Phased rollout timing and A/B testing design are outside the scope of this analysis


# 7. Results Summary:

Output	Value
Total users in cohort	5,998
Total sessions analyzed	49,211
Behavioral metrics engineered	7
Customer segments identified	6
Perks assigned	6 (one per user)
Projected incremental revenue (20% non-booker conversion)	$509,000
Projected reduction in discount spend	~40%



Ubiratan Gonzaga e Silva
DASep2025



## Subtitle 1
Here we talk about the project

## Subtitle 2
