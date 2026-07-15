### Group each user’s events into separate sessions. A new session begins when the time gap between the current event and the user’s previous event is more than 30 minutes. For every session, return its session number, start time, end time, number of events, and duration in minutes.
<img width="1362" height="617" alt="image" src="https://github.com/user-attachments/assets/5a2c3236-e976-48bf-b50b-a1e1d3ca581b" />
<img width="1382" height="776" alt="image" src="https://github.com/user-attachments/assets/733fa4cf-b663-416f-b558-80398e4b71e1" />

### Answer:
WITH event_details AS (
    -- Find the immediately previous event for each user
    SELECT
        event_id,
        user_id,
        event_time,
        LAG(event_time) OVER (
            PARTITION BY user_id
            ORDER BY event_time, event_id
        ) AS previous_event_time
    FROM user_events
),

session_flags AS (
    -- Mark the beginning of every new session
    SELECT
        event_id,
        user_id,
        event_time,
        CASE
            WHEN previous_event_time IS NULL
              OR event_time - previous_event_time > INTERVAL '30 minutes'
            THEN 1
            ELSE 0
        END AS session_flag
    FROM event_details
),

sessionized_events AS (
    -- Cumulatively add session flags to create session numbers
    SELECT
        event_id,
        user_id,
        event_time,
        SUM(session_flag) OVER (
            PARTITION BY user_id
            ORDER BY event_time, event_id
            ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
        ) AS session_number
    FROM session_flags
),

session_summary AS (
    -- Summarize all events belonging to the same session
    SELECT
        user_id,
        session_number,
        MIN(event_time) AS session_start_time,
        MAX(event_time) AS session_end_time,
        COUNT(*) AS event_count
    FROM sessionized_events
    GROUP BY
        user_id,
        session_number
)

SELECT
    user_id,
    session_number,
    session_start_time,
    session_end_time,
    event_count,
    FLOOR(
        EXTRACT(
            EPOCH FROM (session_end_time - session_start_time)
        ) / 60
    )::INTEGER AS duration_minutes
FROM session_summary
ORDER BY
    user_id,
    session_number;

<img width="942" height="637" alt="image" src="https://github.com/user-attachments/assets/7a72dabb-6803-4c93-9007-1813c7266ab6" />


