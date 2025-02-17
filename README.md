# crypto-meme-coin-Analysis
analysis of trump coin

## SQL code for transaction volume on trump and melenia coin
SELECT 
    DATE(block_timestamp) AS transaction_date,  -- Ensure to use the correct timestamp column
    CASE 
        WHEN Mint = '6p6xgHyF7AeE6TZkSmFsko444wqoP15icUSqi2jfGiPN' THEN 'trump_coin'      -- Replace with actual mint address for Trump
        WHEN Mint = 'FUAfBo2jgks6gB4Z4LfZkqSZgzNucisEHqnNebaRxM1P' THEN 'melania_coin'  -- Replace with actual mint address for Melania
    END AS coin_name,
    SUM(Amount) AS total_transaction_volume
FROM solana.core.fact_transfers
WHERE 
    Mint IN ('6p6xgHyF7AeE6TZkSmFsko444wqoP15icUSqi2jfGiPN', 'FUAfBo2jgks6gB4Z4LfZkqSZgzNucisEHqnNebaRxM1P')  -- Replace with actual mint addresses
GROUP BY 
    transaction_date, coin_name
ORDER BY 
    transaction_date;
    
## SQL code for top 10 owners of trump coin

SELECT 
    owner,
    balance
FROM solana.core.fact_token_balances
WHERE 
    Mint = '6p6xgHyF7AeE6TZkSmFsko444wqoP15icUSqi2jfGiPN'  -- Replace with actual mint address for Trump coin
ORDER BY 
    balance DESC
LIMIT 10; 



## SQL code to get total transaction, total balances and avarage prices each day since launch of melenia coin

WITH balance_changes AS (
    SELECT
        block_timestamp::date AS Date,
        SUM(balance) AS total_balances,
        COUNT(tx_id) AS total_transactions
    FROM solana.core.fact_token_balances
    WHERE mint = 'FUAfBo2jgks6gB4Z4LfZkqSZgzNucisEHqnNebaRxM1P'  -- Replace with the actual mint address of Trump Coin
    GROUP BY block_timestamp::date  -- Grouping by date
),
price_data AS (
    SELECT
        hour::date AS time,
        price,
        ROW_NUMBER() OVER (PARTITION BY hour::date ORDER BY hour DESC) AS rn  -- Get the latest price entry for each day
    FROM solana.price.ez_prices_hourly
    WHERE
        hour::date BETWEEN '2025-01-16' AND current_date  -- Ensure this covers the desired date range
        AND TOKEN_ADDRESS = 'FUAfBo2jgks6gB4Z4LfZkqSZgzNucisEHqnNebaRxM1P'
)

-- Select the average price per day considering only the latest price
SELECT
    b.Date,
    b.total_balances,
    b.total_transactions,
    AVG(p.price) AS average_price
FROM
    balance_changes b
JOIN
    price_data p ON b.Date = p.time
WHERE
    p.rn = 1  -- Only include the latest price for each day
GROUP BY
    b.Date, b.total_balances, b.total_transactions  -- Grouping to get the average price
ORDER BY
    b.Date;

## SQL code to get Total supply of trump coin
SELECT 
    SUM(balance) AS total_supply
FROM 
    solana.core.fact_token_balances
WHERE 
    mint = '6p6xgHyF7AeE6TZkSmFsko444wqoP15icUSqi2jfGiPN';

## SQL code to get the total wallets holding the trump coin
SELECT 
    Mint,
    COUNT(account_address) AS number_of_wallets,
    AVG(balance) AS average_balance
FROM solana.core.fact_token_balances
WHERE 
    Mint IN ('6p6xgHyF7AeE6TZkSmFsko444wqoP15icUSqi2jfGiPN', 'FUAfBo2jgks6gB4Z4LfZkqSZgzNucisEHqnNebaRxM1P') 
    -- Replace with actual mint addresses
GROUP BY 
    Mint;

## SQL code to get the Total transaction, total balances, avg prices of trump coin since launch

WITH balance_changes AS (
    SELECT
        block_timestamp::date AS Date,
        SUM(balance) AS total_balances,
        COUNT(tx_id) AS total_transactions
    FROM solana.core.fact_token_balances
    WHERE mint = '6p6xgHyF7AeE6TZkSmFsko444wqoP15icUSqi2jfGiPN'  -- Replace with the actual mint address of Trump Coin
    GROUP BY block_timestamp::date  -- Grouping by date
),
price_data AS (
    SELECT
        hour::date AS time,
        price,
        ROW_NUMBER() OVER (PARTITION BY hour::date ORDER BY hour DESC) AS rn  -- Get the latest price entry for each day
    FROM solana.price.ez_prices_hourly
    WHERE
        hour::date BETWEEN '2025-01-16' AND current_date  -- Ensure this covers the desired date range
        AND TOKEN_ADDRESS = '6p6xgHyF7AeE6TZkSmFsko444wqoP15icUSqi2jfGiPN'
)

-- Select the average price per day considering only the latest price
SELECT
    b.Date,
    b.total_balances,
    b.total_transactions,
    AVG(p.price) AS average_price
FROM
    balance_changes b
JOIN
    price_data p ON b.Date = p.time
WHERE
    p.rn = 1  -- Only include the latest price for each day
GROUP BY
    b.Date, b.total_balances, b.total_transactions  -- Grouping to get the average price
ORDER BY
    b.Date;


## THE DASHBOARD

[PRESIDTIAL COIN DASHBOARD](https://flipsidecrypto.xyz/studio/dashboards/df9f6374-f542-4389-98aa-eca10c4b4a97?beta) 
