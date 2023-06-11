![alt text](https://studiousguy.com/wp-content/uploads/2020/06/mmt1.jpg)

## Introduction

MakeMyTrip is an Indian online travel company that provides online travel services including airline tickets, domestic and international holiday packages, hotel reservations, rail, and bus tickets. In this case study, we will analyze data from MakeMyTrip to gain insights into customer segments, booking behaviors, and trends.

## Input Tables
There are two input tables: `booking_table` and `user_table`.

#### booking_table
| Booking_id | Booking_date | User_id | Line_of_business |
|------------|--------------|---------|------------------|
| b1         | 2022-03-23   | u1      | Flight           |
| b2         | 2022-03-27   | u2      | Flight           |
| ...        | ...          | ...     | ...              |

#### user_table
| User_id | Segment |
|---------|---------|
| u1      | s1      |
| u2      | s1      |
| ...     | ...     |

## Query 1: Segment level analysis for Users who booked flight in april 2022.

### Query
```sql
WITH cte1 AS (
    SELECT
        segment,
        COUNT(DISTINCT b.user_id) AS Users_booked_flight_apr2022
    FROM
        user_table s
        JOIN booking_table b ON s.user_id = b.user_id
    WHERE
        line_of_business = "Flight"
        AND YEAR(Booking_date) = 2022
        AND MONTH(Booking_date) = 04
    GROUP BY
        1
),
cte2 AS (
    SELECT
        segment,
        COUNT(*) AS Total_user_count
    FROM
        user_table
    GROUP BY
        1
)
SELECT
    cte2.segment,
    Total_user_count,
    Users_booked_flight_apr2022
FROM
    cte2
    JOIN cte1 ON cte2.segment = cte1.segment;
```

### Query Output
| Segment | Total_user_count | Users_booked_flight_apr2022 |
|---------|------------------|-----------------------------|
| s1      | 3                | 2                           |
| s2      | 2                | 2                           |
| s3      | 5                | 1                           |

### Analysis
- The first query aims to analyze the number of users in each segment and how many of them booked flights in April 2022.
- Segment s1 has the highest number of users who booked flights in April 2022, followed by s2 and s3.
- This information could be used by MakeMyTrip to target specific segments for promotions or special deals for flight bookings.

## Query 2: Users Whose First Booking was a Hotel Booking

### Query
```sql
SELECT
    *
FROM (
    SELECT
        *,
        ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY Booking_date) AS rn
    FROM
        booking_table
) a
WHERE
    rn = 1
    AND Line_of_business = "Hotel";
```

### Query Output
| Booking_id | Booking_date | User_id | Line_of_business | rn |
|------------|--------------|---------|------------------|----|
| b8         | 2022-04-06   | u6      | Hotel            | 1  |
				

### Analysis
- The second query identifies users whose first booking was for a hotel.
- This information is useful for MakeMyTrip to understand customer preferences and could be used for targeted marketing campaigns towards users who are likely to book hotels.

## Query 3: Days Between First and Last Booking for Each User

### Query
```sql
SELECT
   user_id,  
   max(Booking_date) as last_booking_date, 
   min(Booking_date) as first_booking_date,
   dayofyear(max(Booking_date))-dayofyear(mIN(Booking_date)) as day_diff_lastbooking_firstbooking
FROM Booking_table
GROUP BY 1;
```

### Query Output
| user_id | last_booking_date | first_booking_date | days_diff_lastbooking_firstbooking |
|---------|-------------------|--------------------|------------------------------------|
| u1      | 2022-05-06        | 2022-03-23         | 44                                 |
| u2      | 2022-04-28        | 2022-03-27         | 32                                 |
| u4      | 2022-05-04        | 2022-03-31         | 34                                 |
| u5      | 2022-04-20        | 2022-04-06         | 14                                 |
| u6      | 2022-04-22        | 2022-04-06         | 16                                 |

### Analysis
- The third query calculates the number of days between the first and last booking for each user.
- This information could be used by MakeMyTrip to analyze the booking frequency and activity of each user.
- For example, user `u1` has been actively booking for 44 days, indicating they might be a frequent traveller.

## Query 4: Number of Flight and Hotel Bookings in Each User Segment for the Year 2022

### Query
```sql
SELECT
    segment,
    COUNT(CASE
        WHEN Line_of_business = 'Flight' THEN 1
    END) AS flight_bookings,
    COUNT(CASE
        WHEN Line_of_business = 'Hotel' THEN 1
    END) AS hotel_bookings
FROM
    booking_table b
    JOIN user_table u ON b.user_id = u.user_id
WHERE
    YEAR(booking_date) = 2022
GROUP BY
    segment;
```

### Query Output
| Segment | flight_bookings | hotel_bookings |
|---------|-----------------|----------------|
| s1      | 8               | 4              |
| s2      | 3               | 3              |
| s3      | 1               | 1              |

### Analysis
- The fourth query counts the number of flight and hotel bookings in each user segment for the year 2022.
- Segment `s1` has the highest number of flight bookings, followed by `s2` and `s3`. This indicates that segment `s1` is more inclined toward flying.
- This information is helpful for MakeMyTrip to design segment-specific marketing campaigns.

## Conclusion
This case study involved analysis of user segments, booking behaviors, and trends for MakeMyTrip using SQL. The insights derived from this analysis can help MakeMyTrip in devising effective marketing strategies, understanding user preferences, and potentially increasing bookings through targeted campaigns.


```python

```
