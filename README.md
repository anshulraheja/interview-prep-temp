
## Question
Analyze user engagement on a popular game streaming service using SQL. The goal is to provide insights into user activity based on short and long streams, highlighting how engagement varies.

Requirements:

Use MySQL as the query language.
Analyze data from three tables: streamers, complete_streamers, and viewers.
Output:
The result should include the following columns:

username: The streamer’s username.
total_streams: The total number of streams by each streamer.
avg_stream_duration: The average duration of streams for each streamer, rounded up to the nearest integer (e.g., 20.01 and 20.99 round to 21).
total_viewers: The total number of viewers for all streams by each streamer.
uniq_viewers_gt_30_min: The total number of unique viewers who watched streams for more than 30 minutes.
uniq_viewers_lte_30_min: The total number of unique viewers who watched streams for less than or equal to 30 minutes.
Sorting:

Results should be sorted in ascending order by username.
Additional Notes:

A single stream might have non-unique viewers (e.g., someone watching the same stream on multiple devices).
Ensure the query adheres to the schema of the tables streamers, complete_streamers, and viewers.


## Question

Facebook is receiving a significant number of misinformation reports in Ukraine and Russia following the invasion. How would you ensure that there are enough content reviewers to manage the demand effectively?

Approach:

A. Understand the Problem:

- Identify the types of misinformation circulating on the platform.
- Analyze the volume of users consuming and reporting misinformation.

B. Prioritize Tasks to Address the Issue:

- Update content reviewers with the latest policies specific to the ongoing situation.
- Assign tasks to the most accurate and experienced reviewers to maintain quality.

C. Allocate Tasks Systematically:

- Analyze peak usage times for Facebook during this crisis to determine when misinformation is most active.
- Allocate the maximum number of reviewers during these high-demand periods.
- Assign the best-performing reviewers to work during peak times to handle pressure effectively.
- Ensure 24/7 coverage by organizing shifts and deploying the appropriate number of reviewers in each shift.

D. Monitor and Evaluate the Process:

- Measure the effectiveness of your strategy by comparing pre-implementation and post-implementation metrics.
- Continuously refine the process based on performance data and emerging trends in misinformation.


## Question

Database Schema

Table: trips
| Field           | Data Type |
| --------------- | --------- |
| trip_id         | Integer   |
| trip_timestamp  | Timestamp |
| user_id         | Integer   |
| revenue_usd     | Float     |
| trip_distance_m | Float     |
| trip_time_s     | Integer   |
| market_id       | Integer   |


Table: users
| Field        | Data Type            |
| ------------ | -------------------- |
| user_id      | Integer              |
| user_segment | Enum {top, mid, low} |
| date_joined  | Date                 |


Table: markets
| Field       | Data Type           |
| ----------- | ------------------- |
| market_id   | Integer             |
| market_name | String              |
| market_type | Enum {campus, city} |


**SQL Queries**

Q1: Return all trips in San Francisco and Los Angeles markets

Output: trip_id, trip_timestamp, market_name

```sql
SELECT 
  trip_id, 
  trip_timestamp, 
  market_name 
FROM trips t 
JOIN markets m 
ON t.market_id = m.market_id 
WHERE market_name IN ('Los Angeles', 'San Francisco');
```

Q2: Return the total number of trips and average trip distance by user segment

Output: user_segment, number of trips, average trip distance

```sql
SELECT 
  user_segment, 
  COUNT(t.*) AS "number of trips", 
  AVG(trip_distance_m) AS "average trip distance" 
FROM users u 
JOIN trips t 
ON u.user_id = t.user_id 
GROUP BY user_segment;
```


Q3: Return all markets with more than 5000 trips by "top" users

Output: market_name, number of trips

```sql
SELECT 
  market_name, 
  COUNT(trip_id) AS "number of trips" 
FROM trips t 
JOIN users u 
ON t.user_id = u.user_id 
JOIN markets m 
ON t.market_id = m.market_id 
WHERE user_segment = 'top' 
GROUP BY market_name 
HAVING COUNT(trip_id) > 5000;
```

Q4: Return the total number of trips taken in each market, including 0 for markets with no trips

Output: market_name, number of trips

```sql
SELECT 
  market_name, 
  COUNT(t.trip_id) AS "number of trips" 
FROM markets m 
LEFT JOIN trips t 
ON m.market_id = t.market_id 
GROUP BY market_name;
```

Q5: Return all trips a user has taken, along with their total running revenue

Output: trip_id, user_id, trip_timestamp, total running revenue

```sql
SELECT 
  trip_id, 
  user_id, 
  trip_timestamp, 
  SUM(revenue_usd) OVER (PARTITION BY user_id ORDER BY trip_timestamp) AS "total running revenue" 
FROM trips;
```

Q6: Return the total number of trips every user has taken in every market
For markets where a user has not taken a trip, return 0.

Output: market_name, user_id, total_trips

```sql
SELECT 
  user_id, 
  market_name, 
  COUNT(t.trip_id) AS "total trips" 
FROM markets m 
LEFT JOIN trips t 
ON m.market_id = t.market_id 
LEFT JOIN users u 
ON t.user_id = u.user_id 
GROUP BY user_id, market_name;
```

**Case Study Question**

**Goal:** Improve the trip conversion rate (total trips ÷ total app sessions).

**Key Details:**
- Baseline Conversion Rate: 75%
- Target: 77% (2% improvement)

**Data to Analyze:**
- App opens in the last two weeks
- Trips taken in the last two weeks

**Your Role as an Analyst:**
- Calculate the current conversion rate for the last two weeks.
- Identify patterns or user segments with lower conversion rates.
- Propose actionable suggestions to increase the conversion rate, such as:
- Optimizing the user experience for low-converting segments.
- Offering targeted promotions or incentives for users with fewer trips.
- Improving app performance during peak usage hours.


## Question 
The Good and The Bad City

**Problem Statement:**

A blood bank wants to determine the city with the best and worst donor-to-acceptor blood ratio.

- Best ratio: Maximum donor sum amount / acceptor sum amount.
- Worst ratio: Minimum donor sum amount / acceptor sum amount.
- Both ratios must be unique, and the output must contain exactly two rows with:
- City Name (CITY)
- Ratio (correct to 4 decimal places)

**Schema:**

DONOR Table:

- ID: Integer (Donor ID)
- NAME: String (Donor's name)
- GENDER: Character (Donor's gender)
- CITY: String (Donor's city)
- BLOOD_GROUP: String (Donor's blood group)
- AMOUNT: Integer (Blood amount in pints)

ACCEPTOR Table:

- ID: Integer (Acceptor ID)
- NAME: String (Acceptor's name)
- GENDER: Character (Acceptor's gender)
- CITY: String (Acceptor's city)
- BLOOD_GROUP: String (Acceptor's blood group)
- AMOUNT: Integer (Blood amount needed in pints)

**Steps:**
- Group the data by city in both tables.
- Calculate the total donor blood amount and acceptor blood amount for each city.
- Compute the ratio (donor sum / acceptor sum).
- Identify the city with the highest and lowest ratio.

**Output Format:**
- Each row contains:
  - City Name
  - Ratio (correct to 4 decimal places)

**Example Input/Output:**

Sample Input:

DONOR Table:

| ID  | NAME    | GENDER | CITY                  | BLOOD_GROUP | AMOUNT |
| --- | ------- | ------ | --------------------- | ----------- | ------ |
| 1   | MARIA   | F      | Warne, NH             | AB+         | 7      |
| 2   | DOROTHY | F      | East Natchitoches, PA | AB+         | 3      |

ACCEPTOR Table:

| ID  | NAME  | GENDER | CITY      | BLOOD_GROUP | AMOUNT |
| --- | ----- | ------ | --------- | ----------- | ------ |
| 1   | LINDA | F      | Warne, NH | A+          | 9      |

Sample Output:

```
East Natchitoches, PA 4.5000   Warne, NH 0.3636
```


