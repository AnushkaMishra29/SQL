### For each user, find their longest streak of consecutive login days. Return the start date, end date, and number of days in the longest streak.
<img width="697" height="812" alt="image" src="https://github.com/user-attachments/assets/53c119ea-3bc6-4414-a187-6714697d2e2d" />
<img width="992" height="325" alt="image" src="https://github.com/user-attachments/assets/fc07b7c0-ba99-4e6c-9fd6-b2a2fccf22b5" />


### Answer: 

WITH distinct_logins AS (
    -- Step 1: Treat multiple logins on the same date as one login day
    SELECT DISTINCT
        user_id,
        login_date
    FROM user_logins
),

numbered_logins AS (
    -- Step 2: Number each user's login dates chronologically
    SELECT
        user_id,
        login_date,
        ROW_NUMBER() OVER (
            PARTITION BY user_id
            ORDER BY login_date
        ) AS login_number
    FROM distinct_logins
),

grouped_logins AS (
    -- Step 3: Create a common group identifier for consecutive dates
    SELECT
        user_id,
        login_date,
        login_date - login_number::INTEGER AS streak_group
    FROM numbered_logins
),

streaks AS (
    -- Step 4: Combine all dates belonging to the same streak
    SELECT
        user_id,
        MIN(login_date) AS streak_start_date,
        MAX(login_date) AS streak_end_date,
        COUNT(*) AS streak_days
    FROM grouped_logins
    GROUP BY
        user_id,
        streak_group
),

ranked_streaks AS (
    -- Step 5: Rank each user's streaks from longest to shortest
    SELECT
        user_id,
        streak_start_date,
        streak_end_date,
        streak_days,
        ROW_NUMBER() OVER (
            PARTITION BY user_id
            ORDER BY
                streak_days DESC,
                streak_start_date ASC,
                streak_end_date ASC
        ) AS streak_rank
    FROM streaks
)

SELECT
    user_id,
    streak_start_date,
    streak_end_date,
    streak_days AS longest_streak_days
FROM ranked_streaks
WHERE streak_rank = 1
ORDER BY user_id;

