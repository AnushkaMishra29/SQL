### For each meeting room, find the maximum number of confirmed bookings active at the same time and the earliest timestamp when this maximum was first reached.
<img width="1512" height="767" alt="image" src="https://github.com/user-attachments/assets/51693a04-cc6e-4d4b-a46f-746ba8269777" />
<img width="1071" height="302" alt="image" src="https://github.com/user-attachments/assets/fc7ad3e8-de0c-4f2f-b8cf-79da5cd8474f" />


### ANSWER:
WITH booking_events AS (
######    -- Every booking start increases concurrency by 1
    SELECT
        room_id,
        start_time AS event_time,
        1 AS booking_change
    FROM room_bookings
    WHERE booking_status = 'confirmed'

    UNION ALL

######     -- Every booking end decreases concurrency by 1
    SELECT
        room_id,
        end_time AS event_time,
        -1 AS booking_change
    FROM room_bookings
    WHERE booking_status = 'confirmed'
),

events_per_timestamp AS (
######     -- Combine all starts and ends occurring at the same timestamp
    SELECT
        room_id,
        event_time,
        SUM(booking_change) AS net_booking_change
    FROM booking_events
    GROUP BY
        room_id,
        event_time
),

running_concurrency AS (
######     -- Calculate how many bookings are active after each timestamp
    SELECT
        room_id,
        event_time,
        SUM(net_booking_change) OVER (
            PARTITION BY room_id
            ORDER BY event_time
            ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
        ) AS concurrent_bookings
    FROM events_per_timestamp
),

ranked_peaks AS (
######     -- Rank highest concurrency first and earliest timestamp first
    SELECT
        room_id,
        event_time,
        concurrent_bookings,
        ROW_NUMBER() OVER (
            PARTITION BY room_id
            ORDER BY
                concurrent_bookings DESC,
                event_time ASC
        ) AS peak_rank
    FROM running_concurrency
)

SELECT
    room_id,
    concurrent_bookings AS peak_concurrent_bookings,
    event_time AS peak_start_time
FROM ranked_peaks
WHERE peak_rank = 1
ORDER BY room_id;

<img width="1022" height="342" alt="image" src="https://github.com/user-attachments/assets/5b3feccc-8adf-45f3-94ee-09f4bb7edf94" />

<img width="1251" height="600" alt="image" src="https://github.com/user-attachments/assets/15797a0d-7f38-444b-8d32-cd54d9fb8376" />


