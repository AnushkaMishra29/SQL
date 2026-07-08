## 1. What business question does LAST_VALUE() solve?

LAST_VALUE() answers:

“What is the latest value in this user/customer/order journey?”

It is useful for questions like:

“Show every order along with the customer’s latest order status.”

“Show every user event along with the user’s latest activity date.”

“Show every subscription row along with the user’s latest plan.”

“Show every support ticket update along with the latest ticket status.”

## 2. Why LAST_VALUE() is tricky

Unlike FIRST_VALUE(), LAST_VALUE() often gives unexpected results if you do not define the window frame properly.

Many beginners write:

LAST_VALUE(event_name) OVER (
    PARTITION BY user_id
    ORDER BY event_time
) AS last_event_name

But in many SQL engines, this returns the current row’s event, not the actual last event of the user.

Why?

# Because the default frame often means: ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW

## Correct version using frame
SELECT
    user_id,
    event_time,
    event_name,

    -- Latest event name in the full user journey
    LAST_VALUE(event_name) OVER (
        PARTITION BY user_id
        ORDER BY event_time ASC, event_id ASC
#        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS last_event_name,

    -- Latest event time in the full user journey
    LAST_VALUE(event_time) OVER (
        PARTITION BY user_id
        ORDER BY event_time ASC, event_id ASC
#        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS last_event_time

FROM events
ORDER BY
    user_id,
    event_time,
    event_id;

ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW

So SQL only looks from the first row up to the current row, not till the last row of the user.
