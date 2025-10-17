# U.S-flight-delay-analysis-2015
-- Creating tables 'flights'

CREATE TABLE flights (
    AIRLINE VARCHAR(2),
    FLIGHT_NUMBER INT,
    TAIL_NUMBER VARCHAR(10),
    ORIGIN_AIRPORT VARCHAR(10),
    DESTINATION_AIRPORT VARCHAR(10),
    SCHEDULED_DEPARTURE INT,
    DEPARTURE_TIME INT,
    DEPARTURE_DELAY INT,
    TAXI_OUT INT,
    WHEELS_OFF INT,
    SCHEDULED_TIME INT,
    ELAPSED_TIME INT,
    AIR_TIME INT,
    DISTANCE INT,
    WHEELS_ON INT,
    TAXI_IN INT,
    SCHEDULED_ARRIVAL INT,
    ARRIVAL_TIME INT,
    ARRIVAL_DELAY INT,
    DIVERTED BOOLEAN,
    CANCELLED BOOLEAN,
    CANCELLATION_REASON VARCHAR(10),
    AIR_SYSTEM_DELAY INT,
    SECURITY_DELAY INT,
    AIRLINE_DELAY INT,
    LATE_AIRCRAFT_DELAY INT,
    WEATHER_DELAY INT
);

-- Creating 'airlines' table
CREATE TABLE airlines (
    IATA_CODE VARCHAR(2) ,
    AIRLINE VARCHAR(100)
);

-- Creating 'airports' table
CREATE TABLE airports (
    IATA_CODE VARCHAR(3),
    AIRPORT VARCHAR(100),
    CITY VARCHAR(100),
    STATE VARCHAR(2),
    COUNTRY VARCHAR(3),
    LATITUDE FLOAT,
    LONGITUDE FLOAT
);



-- Checking current data types

SELECT 
	column_name, 
	data_type
FROM information_schema.columns
WHERE table_name = 'flights'

-- changing data types in TIME and Intervals  For TIME 
-- scheduled_departure, departure_time, wheels_off, scheduled_arrival, arrival_time, wheels_on

ALTER TABLE 
			flights
ALTER COLUMN 
			SCHEDULED_DEPARTURE TYPE TIME USING 
			(TO_TIMESTAMP(LPAD(SCHEDULED_DEPARTURE::text, 4, '0'), 'HH24MI')::TIME);


ALTER TABLE 
			flights
ALTER COLUMN 
			DEPARTURE_TIME TYPE TIME USING 
			(TO_TIMESTAMP(LPAD(DEPARTURE_TIME::text, 4, '0'), 'HH24MI')::TIME);


ALTER TABLE
			flights 
ALTER COLUMN
			WHEELS_OFF TYPE TIME USING
			(TO_TIMESTAMP(LPAD(WHEELS_OFF::text, 4, '0'), 'HH24MI')::TIME);


ALTER TABLE
			flights 
ALTER COLUMN 
			SCHEDULED_ARRIVAL TYPE TIME USING
			(TO_TIMESTAMP(LPAD(SCHEDULED_ARRIVAL::text, 4, '0'), 'HH24MI')::TIME);


ALTER TABLE 
			flights 
ALTER COLUMN 
			ARRIVAL_TIME TYPE TIME USING 
			(TO_TIMESTAMP(LPAD(ARRIVAL_TIME::text, 4, '0'), 'HH24MI')::TIME);


ALTER TABLE 
			flights
ALTER COLUMN 
			WHEELS_ON TYPE TIME USING 
			(TO_TIMESTAMP(LPAD(WHEELS_ON::text, 4, '0'), 'HH24MI')::TIME);


-- FOR INTERVAL
-- departure_delay, arrival_delay, taxi_out, taxi_in, elapsed_time, air_time

-- For departure_delay
ALTER TABLE
			flights 
ALTER COLUMN 
			DEPARTURE_DELAY TYPE INTERVAL USING 
			(DEPARTURE_DELAY * INTERVAL '1 minute');


ALTER TABLE 
			flights 
ALTER COLUMN
			ARRIVAL_DELAY TYPE INTERVAL USING 
			(ARRIVAL_DELAY * INTERVAL '1 minute');


ALTER TABLE 
			flights 
ALTER COLUMN 
			TAXI_OUT TYPE INTERVAL USING 
			(TAXI_OUT * INTERVAL '1 minute');


ALTER TABLE 
			flights
ALTER COLUMN
			TAXI_IN TYPE INTERVAL USING
			(TAXI_IN * INTERVAL '1 minute');


ALTER TABLE 
			flights
ALTER COLUMN 
			ELAPSED_TIME TYPE INTERVAL USING 
			(ELAPSED_TIME * INTERVAL '1 minute');


ALTER TABLE 
			flights 
ALTER COLUMN 
			AIR_TIME TYPE INTERVAL USING
			(AIR_TIME * INTERVAL '1 minute');

-- Creating column flight_datetime BY (year , month , day) Column  and extracting hour and minute  from (scheduled_departure) column.

ALTER TABLE flights
ADD COLUMN flight_datetime TIMESTAMP;

UPDATE flights
SET flight_datetime = 
    make_timestamp(year, month, day,
                   EXTRACT(HOUR FROM scheduled_departure),
                   EXTRACT(MINUTE FROM scheduled_departure),
                   0);

-- Checking null values 

SELECT
    SUM(CASE WHEN AIRLINE IS NULL THEN 1 ELSE 0 END) AS airline_nulls,
    SUM(CASE WHEN FLIGHT_NUMBER IS NULL THEN 1 ELSE 0 END) AS flight_number_nulls,
    SUM(CASE WHEN TAIL_NUMBER IS NULL THEN 1 ELSE 0 END) AS tail_number_nulls,
    SUM(CASE WHEN ORIGIN_AIRPORT IS NULL THEN 1 ELSE 0 END) AS origin_airport_nulls,
    SUM(CASE WHEN DESTINATION_AIRPORT IS NULL THEN 1 ELSE 0 END) AS destination_airport_nulls,
    SUM(CASE WHEN SCHEDULED_DEPARTURE IS NULL THEN 1 ELSE 0 END) AS scheduled_departure_nulls,
    SUM(CASE WHEN DEPARTURE_TIME IS NULL THEN 1 ELSE 0 END) AS departure_time_nulls,
    SUM(CASE WHEN DEPARTURE_DELAY IS NULL THEN 1 ELSE 0 END) AS departure_delay_nulls,
    SUM(CASE WHEN TAXI_OUT IS NULL THEN 1 ELSE 0 END) AS taxi_out_nulls,
    SUM(CASE WHEN WHEELS_OFF IS NULL THEN 1 ELSE 0 END) AS wheels_off_nulls,
    SUM(CASE WHEN SCHEDULED_TIME IS NULL THEN 1 ELSE 0 END) AS scheduled_time_nulls,
    SUM(CASE WHEN ELAPSED_TIME IS NULL THEN 1 ELSE 0 END) AS elapsed_time_nulls,
    SUM(CASE WHEN AIR_TIME IS NULL THEN 1 ELSE 0 END) AS air_time_nulls,
    SUM(CASE WHEN DISTANCE IS NULL THEN 1 ELSE 0 END) AS distance_nulls,
    SUM(CASE WHEN WHEELS_ON IS NULL THEN 1 ELSE 0 END) AS wheels_on_nulls,
    SUM(CASE WHEN TAXI_IN IS NULL THEN 1 ELSE 0 END) AS taxi_in_nulls,
    SUM(CASE WHEN SCHEDULED_ARRIVAL IS NULL THEN 1 ELSE 0 END) AS scheduled_arrival_nulls,
    SUM(CASE WHEN ARRIVAL_TIME IS NULL THEN 1 ELSE 0 END) AS arrival_time_nulls,
    SUM(CASE WHEN ARRIVAL_DELAY IS NULL THEN 1 ELSE 0 END) AS arrival_delay_nulls,
    SUM(CASE WHEN DIVERTED IS NULL THEN 1 ELSE 0 END) AS diverted_nulls,
    SUM(CASE WHEN CANCELLED IS NULL THEN 1 ELSE 0 END) AS cancelled_nulls,
    SUM(CASE WHEN CANCELLATION_REASON IS NULL THEN 1 ELSE 0 END) AS cancellation_reason_nulls,
    SUM(CASE WHEN AIR_SYSTEM_DELAY IS NULL THEN 1 ELSE 0 END) AS air_system_delay_nulls,
    SUM(CASE WHEN SECURITY_DELAY IS NULL THEN 1 ELSE 0 END) AS security_delay_nulls,
    SUM(CASE WHEN AIRLINE_DELAY IS NULL THEN 1 ELSE 0 END) AS airline_delay_nulls,
    SUM(CASE WHEN LATE_AIRCRAFT_DELAY IS NULL THEN 1 ELSE 0 END) AS late_aircraft_delay_nulls,
    SUM(CASE WHEN WEATHER_DELAY IS NULL THEN 1 ELSE 0 END) AS weather_delay_nulls
FROM
    flights;

-- filling null values ( this will fill all the null values which column needed  )

UPDATE flights
SET 
    cancelled = COALESCE(cancelled, FALSE),
    diverted = COALESCE(diverted, FALSE),
    air_system_delay = COALESCE(air_system_delay, 0),
    security_delay = COALESCE(security_delay, 0),
    airline_delay = COALESCE(airline_delay, 0),
    late_aircraft_delay = COALESCE(late_aircraft_delay, 0),
    weather_delay = COALESCE(weather_delay, 0),
	departure_delay = COALESCE(departure_delay, INTERVAL '0 minutes'),
    arrival_delay = COALESCE(arrival_delay, INTERVAL '0 minutes'),
    taxi_out = COALESCE(taxi_out, INTERVAL '0 minutes'),
    taxi_in = COALESCE(taxi_in, INTERVAL '0 minutes'),
    elapsed_time = COALESCE(elapsed_time, INTERVAL '0 minutes'),
    air_time = COALESCE(air_time, INTERVAL '0 minutes'),

    distance = COALESCE(distance, 0),
	
    cancellation_reason = COALESCE(cancellation_reason, 'No Reason'),
    airline = COALESCE(airline, 'Unknown Airline'),
    origin_airport = COALESCE(origin_airport, 'Unknown Origin'),
    destination_airport = COALESCE(destination_airport, 'Unknown Destination');



-- Creating cacellion reason desripiton column for treating by describing the reason 



UPDATE flights
SET cancelled = CASE 
	WHEN cancelled = 1 THEN TRUE ELSE FALSE END;

ALTER TABLE flights
ADD COLUMN cancellation_reason TEXT;

UPDATE flights
SET cancellation_reason_desc = CASE
    WHEN cancellation_reason = 'A' THEN 'Carrier'
    WHEN cancellation_reason = 'B' THEN 'Weather'
    WHEN cancellation_reason = 'C' THEN 'National Air System'
    WHEN cancellation_reason = 'D' THEN 'Security'
    ELSE 'Unknown'
END;


-- joining tables  which is very important.


CREATE TABLE flights_cleaned AS
SELECT
 --  FLIGHT IDENTIFIERS
 	f.day_of_week,
    f.flight_datetime, 
    f.flight_number,
    f.tail_number,
    f.airline,
    al.airline AS airline_name,
    f.origin_airport,
    ao.airport  AS origin_airport_name,
    ao.city     AS origin_city,
    ao.state    AS origin_state,
    ao.country  AS origin_country,
    f.destination_airport,
    ad.airport  AS destination_airport_name,
    ad.city     AS destination_city,
    ad.state    AS destination_state,
    ad.country  AS destination_country,
 -- DATE / TIME HANDLING    
    f.scheduled_departure,
    f.departure_time,
    f.departure_delay,
    f.taxi_out,
    f.wheels_off,
    f.scheduled_time,
    f.elapsed_time,
    f.air_time,
    f.distance,
    f.wheels_on,
    f.taxi_in,
    f.scheduled_arrival,
    f.arrival_time,
    f.arrival_delay,
 -- STATUS FLAGS
    f.diverted,
    f.cancelled,
    f.cancellation_reason,
    f.cancellation_reason_cause,
-- date and time dealay
    f.air_system_delay,
    f.security_delay,
    f.airline_delay,
    f.late_aircraft_delay,
    f.weather_delay

FROM flights f
LEFT JOIN airlines al 
    ON UPPER(TRIM(f.airline)) = UPPER(TRIM(al.iata_code))
LEFT JOIN airports ao 
    ON UPPER(TRIM(f.origin_airport)) = UPPER(TRIM(ao.iata_code))
LEFT JOIN airports ad 
    ON UPPER(TRIM(f.destination_airport)) = UPPER(TRIM(ad.iata_code))
ORDER BY f.flight_datetime;

-- VERIFYING THE TABLE

SELECT column_name , data_type
FROM information_schema.columns
WHERE table_name = 'flights_cleaned'
ORDER BY ordinal_position;


-- EDA query


SELECT 
COUNT(*) AS total_flights,
COUNT(*) FILTER (WHERE cancelled = TRUE) AS cancelled_flights,
COUNT(*) FILTER (WHERE diverted = TRUE) AS diverted_flights,
ROUND(100.0 * COUNT(*) FILTER(WHERE cancelled = TRUE)/COUNT(*), 2)AS cancellation_rate_pct,
ROUND(100.0 * COUNT(*) FILTER(WHERE diverted = TRUE)/ COUNT(*), 2) AS diversion_rate_pct
FROM flights_cleaned;


-- 2. Cancellation by Reason

SELECT 
cancellation_reason,
cancellation_reason_cause,
COUNT(*) AS cancelled_flights,
ROUND(100.0 * COUNT(*) / (SELECT COUNT(*) FROM flights_cleaned WHERE cancelled = TRUE), 2) AS pct_of_cancellations
FROM flights_cleaned
WHERE cancelled = TRUE
GROUP BY cancellation_reason , cancellation_reason_cause
ORDER BY cancelled_flights DESC;


-- 3. Basic Statistics for departure and arrival delays

SELECT 
    ROUND(AVG(EXTRACT(EPOCH FROM departure_delay) / 60), 2) AS avg_departure_delay_min,
    ROUND(CAST(PERCENTILE_CONT(0.5) 
          WITHIN GROUP (ORDER BY EXTRACT(EPOCH FROM departure_delay) / 60) AS numeric), 2) AS median_departure_delay_min,
    ROUND(MIN(EXTRACT(EPOCH FROM departure_delay) / 60), 2) AS min_departure_delay_min,
    ROUND(MAX(EXTRACT(EPOCH FROM departure_delay) / 60), 2) AS max_departure_delay_min,

    ROUND(AVG(EXTRACT(EPOCH FROM arrival_delay) / 60), 2) AS avg_arrival_delay_min,
    ROUND(CAST(PERCENTILE_CONT(0.5) 
          WITHIN GROUP (ORDER BY EXTRACT(EPOCH FROM arrival_delay) / 60) AS numeric), 2) AS median_arrival_delay_min,
    ROUND(MIN(EXTRACT(EPOCH FROM arrival_delay) / 60), 2) AS min_arrival_delay_min,
    ROUND(MAX(EXTRACT(EPOCH FROM arrival_delay) / 60), 2) AS max_arrival_delay_min
FROM flights_cleaned
WHERE cancelled = FALSE;

-- 	4. 	Distribution of diffirent types of delays i.e 
--			(air system delay , security delay , airline delay , late aircraft delay, weather delay)

SELECT 
	ROUND(SUM(air_system_delay), 2) AS total_air_system_delay,
	ROUND(SUM(security_delay), 2) AS total_security_delay,
	ROUND(SUM(airline_delay), 2) AS total_airline_delay,
	ROUND(SUM(late_aircraft_delay), 2) AS total_late_aircraft_delay,
	ROUND(SUM(weather_delay), 2) AS total_weather_delay
FROM flights_cleaned;

-- 4(a) delays in percentage

SELECT
	'air system delay' AS delay_type,
	ROUND(100.0 * SUM(air_system_delay) / 
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned
UNION ALL
SELECT
	'security delay' AS delay_type,
	ROUND(100.0 * SUM(security_delay) / 
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned
UNION ALL
SELECT
	'airline delay' AS delay_type,
	ROUND(100.0 * SUM(airline_delay) / 
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned
UNION ALL
SELECT
	'late aircraft delay' AS delay_type,
	ROUND(100.0 * SUM(late_aircraft_delay) /
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned
UNION ALL
SELECT
	'weather delay' AS delay_type,
	ROUND(100.0 * SUM(weather_delay) / 
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned ;


--- KPI 

-- 1. OTP ( ON TIME PERFORMANCE ) 15 mins of scheduled time 

SELECT 
    ROUND(
        100.0 * COUNT(*) FILTER (WHERE EXTRACT(EPOCH FROM arrival_delay) / 60 <= 15)
        / COUNT(*), 2) AS otp_rate_percentage
FROM flights_cleaned
WHERE cancelled = FALSE;

-- 2.  average  and departure delay KPI's

SELECT 
	ROUND(AVG(EXTRACT(EPOCH FROM arrival_delay) / 60), 2) AS avg_arrival_delay_min,
	ROUND(AVG(EXTRACT(EPOCH FROM departure_delay) / 60), 2) AS avg_departure_delay_min
FROM flights_cleaned
WHERE cancelled = FALSE;

-- 3. Cancellation rate (%)

SELECT 
	ROUND(100.0 * COUNT(*) FILTER (WHERE cancelled = TRUE) / COUNT(*), 2) AS cancelled_rate_percent
FROM flights_cleaned;

-- 4.	Percentage contribution of each delay type

SELECT
	'air system delay' AS delay_type,
	ROUND(100.0 * SUM(air_system_delay) / 
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned
UNION ALL
SELECT
	'security delay' AS delay_type,
	ROUND(100.0 * SUM(security_delay) / 
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned
UNION ALL
SELECT
	'airline delay' AS delay_type,
	ROUND(100.0 * SUM(airline_delay) / 
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned
UNION ALL
SELECT
	'late aircraft delay' AS delay_type,
	ROUND(100.0 * SUM(late_aircraft_delay) /
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned
UNION ALL
SELECT
	'weather delay' AS delay_type,
	ROUND(100.0 * SUM(weather_delay) / 
		SUM(air_system_delay + security_delay + airline_delay + late_aircraft_delay + weather_delay), 2) AS percentage_delay
FROM flights_cleaned ;


-- Innitail aggregation KPI 


SELECT 
	airline_name,
	COUNT(*) AS total_flights,
	ROUND(AVG(EXTRACT(EPOCH FROM departure_delay) / 60), 2)AS avg_dep_delay,
	ROUND(AVG(EXTRACT(EPOCH FROM arrival_delay)/ 60), 2) AS avg_arrival_delay,
	ROUND( 100.0 * COUNT(*) FILTER (WHERE EXTRACT(EPOCH FROM arrival_delay) / 60 <= 15) / COUNT(*), 2) AS otp_rate,
	ROUND(100.0 * COUNT(*) FILTER(WHERE cancelled = TRUE)/ COUNT(*), 2) AS cancelled_rate
FROM flights_cleaned
GROUP BY airline_name
ORDER BY otp_rate DESC;

-- 2. BY Airport

SELECT 
	origin_airport_name,
	COUNT(*) AS total_departures,
	ROUND(AVG(EXTRACT(EPOCH FROM departure_delay) / 60), 2) AS avg_departure_delay,
	ROUND(100.0 * COUNT(*) FILTER( WHERE EXTRACT(EPOCH FROM departure_delay) / 60 <= 15) / COUNT (*), 2) AS otp_rate
FROM flights_cleaned
GROUP BY origin_airport_name
ORDER BY avg_departure_delay DESC;



-- 3.(a) By month

SELECT 
	TO_CHAR(flight_datetime,'Month') AS month_name,
	ROUND(AVG(EXTRACT(EPOCH FROM arrival_delay) / 60), 2) AS avg_arrival_delay,
	ROUND(AVG(EXTRACT(EPOCH FROM departure_delay)/ 60), 2) AS avg_departure_delay,
	ROUND(100.0 * COUNT(*) FILTER( WHERE EXTRACT( EPOCH FROM arrival_delay)/ 60 <= 15) / COUNT(*), 2) AS otp_rate
FROM flights_cleaned
GROUP BY TO_CHAR(flight_datetime,'Month'), EXTRACT(MONTH FROM flight_datetime)
ORDER BY EXTRACT(MONTH FROM flight_datetime);


-- 3.(b)  BY day of week

SELECT 
    CASE 
        WHEN day_of_week = 1 THEN 'Monday'
        WHEN day_of_week = 2 THEN 'Tuesday'
        WHEN day_of_week = 3 THEN 'Wednesday'
        WHEN day_of_week = 4 THEN 'Thursday'
        WHEN day_of_week = 5 THEN 'Friday'
        WHEN day_of_week = 6 THEN 'Saturday'
        WHEN day_of_week = 7 THEN 'Sunday'
    END AS day_name,
    ROUND(AVG(EXTRACT(EPOCH FROM arrival_delay) / 60), 2) AS avg_arrival_delay_min,
    ROUND(AVG(EXTRACT(EPOCH FROM departure_delay) / 60), 2) AS avg_departure_delay_min,
    ROUND(100.0 * COUNT(*) FILTER (WHERE EXTRACT(EPOCH FROM arrival_delay) / 60 <= 15) / COUNT(*), 2) AS otp_rate_percent
FROM flights_cleaned
WHERE cancelled = FALSE
GROUP BY day_of_week
ORDER BY day_of_week;

-- 3.(c) BY time of day

SELECT 
CASE 
	WHEN EXTRACT(HOUR FROM flight_datetime) BETWEEN 0 AND 5 THEN 'Night'
	WHEN EXTRACT(HOUR FROM flight_datetime) BETWEEN 6 AND 11 THEN 'Morning'
	WHEN EXTRACT(HOUR FROM flight_datetime) BETWEEN 12 AND 17 THEN 'Afternoon'
	ELSE 'Evening'
END AS time_of_day,
	ROUND(AVG(EXTRACT(EPOCH FROM arrival_delay) / 60), 2) AS avg_arrival_delay,
	ROUND(AVG(EXTRACT(EPOCH FROM departure_delay)/ 60), 2) AS avg_departure_delay,
	ROUND(100.0 * COUNT(*) FILTER( WHERE EXTRACT( EPOCH FROM arrival_delay)/ 60 <= 15) / COUNT(*), 2) AS otp_rate
FROM flights_cleaned
GROUP BY time_of_day
ORDER BY avg_arrival_delay, avg_departure_delay;








				   



