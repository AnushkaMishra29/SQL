### For each stock, find the best date to buy, the best later date to sell, and the maximum possible profit from exactly one buy-and-sell transaction. Stocks that never produce a positive profit must still appear in the output.
<img width="982" height="800" alt="image" src="https://github.com/user-attachments/assets/e19a6d45-b1f5-4cf2-9ca5-b40808db85be" />
<img width="952" height="257" alt="image" src="https://github.com/user-attachments/assets/29c7d975-2b41-48cb-b4b8-2ca921a5c512" />
#### Conditions:
#### The stock must be bought before it is sold.
#### The buy and sell records must belong to the same stock.
#### Only one buy-and-sell transaction is allowed per stock.
#### Short selling is not allowed.
#### Return every distinct stock, even when no profitable transaction exists.
#### When no positive profit is possible, return NULL for both dates and 0.00 as the maximum profit.
#### When multiple transactions produce the same maximum profit, choose the earliest buy_date.
#### If the tied transactions also have the same buy_date, choose the earliest sell_date.
#### Assume that each stock has at most one price record per date.
#### Sort the output by stock_symbol.

WITH all_stocks AS (
###    SELECT DISTINCT
        stock_symbol
    FROM stock_prices
),

all_possible_transactions AS (
    SELECT
        buy.stock_symbol AS stock_symbol,
        buy.price_date AS buy_date,
        sell.price_date AS sell_date,
###        sell.closing_price - buy.closing_price AS profit
    FROM stock_prices AS buy
    JOIN stock_prices AS sell
        ON buy.stock_symbol = sell.stock_symbol
       AND buy.price_date < sell.price_date
),

ranking AS (
    SELECT
        stock_symbol,
        buy_date,
        sell_date,
        profit,
###        ROW_NUMBER() OVER (
            PARTITION BY stock_symbol
            ORDER BY
###                profit DESC,
###                buy_date ASC,
###                sell_date ASC
        ) AS transaction_rank
    FROM all_possible_transactions
)

SELECT
    st.stock_symbol,

    CASE
###        WHEN r.profit > 0 THEN r.buy_date
        ELSE NULL
    END AS buy_date,

    CASE
###        WHEN r.profit > 0 THEN r.sell_date
        ELSE NULL
    END AS sell_date,

    CASE
 ###       WHEN r.profit > 0 THEN r.profit
        ELSE 0.00
    END AS maximum_profit

FROM all_stocks AS st
LEFT JOIN ranking AS r
    ON st.stock_symbol = r.stock_symbol
###   AND r.transaction_rank = 1

ORDER BY
    st.stock_symbol;


