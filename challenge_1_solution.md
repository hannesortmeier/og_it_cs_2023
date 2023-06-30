# IT-CS 2023 - Bei 100 Mrd. Clicksteam-Events & Backend-Daten den Überblick wahren? Mit SQL geht’s - Eine Coding-Challenge
## Challenge 1

Jedesmal, wenn ein User ein Produkt in einem der Otto Group online Shops anschaut oder eine Produktliste angezeigt bekommt, wird ein Eintrag
in der Tabelle `it_cs_2023_display_product_details` (7,194,934,727 Zeilen) bzw `it_cs_2023_display_product_list` (169,464,595,684  Zeilen)
erzeugt.

Berechne für alle Produkte die Anzahl der eindeutigen Nutzer, die sich das Produkt auf einer Detailseite/Produktliste am 04.03.2023 und
05.03.2023 angeschaut haben.


So könnte der Output einer Lösung aussehen:

| productID         | date                    | viewCount |
| ----------------- | ----------------------- | --------- |
| EC0101-S0I0S0RT   | 2023-05-01 00:00:00 UTC | 524       |
| EC0101-759068328  | 2023-05-01 00:00:00 UTC | 202       |
| EC0101-1160556334 | 2023-05-01 00:00:00 UTC | 3404      |
| EC0101-1222277937 | 2023-05-02 00:00:00 UTC | 689       |


## Lösungsbeispiel

```SQL
WITH product_view AS (
  -- Get all product related events and discard duplicates
  SELECT *
  FROM it_cs_2023_display_product_details
  UNION ALL
  SELECT *
  FROM it_cs_2023_display_product_list
)

-- Calculate count of unique viewers per productId and day
SELECT 
  productId, 
  -- SQLite: DATE(eventTime, 'unixepoch') AS date,
  TIMESTAMP_TRUNC(eventTime, DAY) AS date, 
  COUNT(DISTINCT userId)
FROM product_view
-- SQLite: WHERE DATE(eventTime, 'unixepoch') IN ('2023-03-04', '2023-03-05')
WHERE TIMESTAMP_TRUNC(eventTime, DAY) IN ('2023-03-04', '2023-03-05')
GROUP BY 1, 2
```