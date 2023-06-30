# IT-CS 2023 - Bei 100 Mrd. Clicksteam-Events & Backend-Daten den Überblick wahren? Mit SQL geht’s - Eine Coding-Challenge
## Challenge 3

Jedesmal, wenn ein User ein oder mehrere Produkte in einem der Otto Group online Shops kauft, wird ein
Eintrag in der Tabelle `it_cs_2023_purchase` erzeugt. Die Tabelle `it_cs_2023_purchase`
hat 152,335,614 Einträge aus den letzten 12 Monaten.

Berechne für jeden User die wöchentliche Anzahl der Käufe, sowie die wöchentliche Anzahl der Käufe in
den jeweils vorherigen vier Wochen.


So könnte der Output einer Lösung aussehen:

| userId                     | cw  | week_1 | week_2 | week_3 | week_4 | week_5 |
| --------------------------- | --- | :----: | :----: | :----: | :----: | :----: |
| 07be35e7c3c9b8aa828526a5... | 8   |   1    |   0    |   0    |   1    |   0    |
| 0fda16d3332ec867d4c1d923... | 8   |   0    |   4    |   1    |   1    |   1    |
| 1ee5adcce2da977539e03814... | 8   |   0    |   0    |   0    |   1    |   0    |
| 3dfcb0be70a89abeb201d4f5... | 8   |   7    |   1    |   1    |   1    |   8    |
| a84cf32a55af25dafcb2de14... | 42  |   1    |   10   |   0    |   11   |   0    |
| 9a1c52eada3e02f333ce55ca... | 37  |   0    |   0    |   0    |   2    |   9    |


## Lösungsvorschlag

```SQL
-- SQLite version
WITH RECURSIVE weeks(cw) AS (
    SELECT 1
    UNION ALL
    SELECT cw+1 FROM weeks
                         LIMIT 52
    )
   , base AS (
-- Generate 52 entries for every calendar week for every userId
SELECT DISTINCT userId, weeks.cw
FROM it_cs_2023_purchase, weeks
    )

        , purchase AS (
    -- Calculate the number of purchases for every userId for every calendar week
SELECT
    userId,
    -- EXTRACT(WEEK FROM eventTime) AS cw, 
    CAST(strftime('%W',DATE(eventtime, 'unixepoch')) as INTEGER) as cw,
    COUNT(*) AS count_purchase
FROM it_cs_2023_purchase
GROUP BY 1,2
    )
-- Calculate purchase counts of previous 4 calendar weeks
SELECT
    userId,
    cw,
    count_purchase as week_0,
    LAG(count_purchase, 1) OVER t AS week_1,
    LAG(count_purchase, 2) OVER t AS week_2,
    LAG(count_purchase, 3) OVER t AS week_3,
    LAG(count_purchase, 4) OVER t AS week_4,
    LAG(count_purchase, 5) OVER t AS week_5
FROM base
         LEFT JOIN purchase USING (userId, cw)
WHERE cw = 24
WINDOW t AS (PARTITION BY userId ORDER BY cw);


-- BigQuery version
WITH base AS (
  -- Generate 52 entries for every calendar week for every userId
  SELECT DISTINCT userId, cw
  FROM it_cs_2023_purchase, UNNEST(GENERATE_ARRAY(1,52)) AS cw
)

, purchase AS (
  -- Calculate the number of purchases for every userId for every calendar week
  SELECT 
    userId, 
    EXTRACT(WEEK FROM eventTime) AS cw, 
    COUNT(*) AS count_purchase
  FROM it_cs_2023_purchase
  GROUP BY 1,2
)

-- Calculate purchase counts of previous 4 calendar weeks
SELECT
  userId,
  cw,
  LAG(count_purchase, 1) OVER t AS week_1,
  LAG(count_purchase, 2) OVER t AS week_2,
  LAG(count_purchase, 3) OVER t AS week_3,
  LAG(count_purchase, 4) OVER t AS week_4,
  LAG(count_purchase, 5) OVER t AS week_5,
FROM base
LEFT JOIN purchase USING (userId, cw)
WINDOW t AS (PARTITION BY userId ORDER BY cw)
```