# IT-CS 2023 - Bei 100 Mrd. Clicksteam-Events & Backend-Daten den Überblick wahren? Mit SQL geht’s - Eine Coding-Challenge
## Challenge 2

Täglich liefern alle OTTO Group Gesellschaften ihren aktuellen Stand an Produktstammdaten an unseren Datenpool.
In der Tabelle `it_cs_2023_skufeed` befinden sich alle Produktstammdaten von Otto aus den letzten 12 Monaten.
Die Tabelle beinhaltet 17,633,601,647 Einträge.

Berechne den aktuellsten Stand für jede SKU (**S**tock**K**eeping**U**nit, eindeutig identifiziert über die skuID) aus den
Produktstammdaten (`it_cs_2023_skufeed`) für den Monat Februar. Ignoriere die Produkte aus der Tabelle
`it_cs_2023_ignored_products`.


So könnte der Output einer Lösung aussehen:

| productID              | skuID                           | productName                                                              | loadDate   |
| ---------------------- | ------------------------------- | ------------------------------------------------------------------------ | ---------- |
| EC0101-1098453998      | EC0101-1098488224               | SO NOS Jeans Keith                                                       | 2023-12-31 |
| EC0101-1546575890      | EC0101-1546575895               | Tapered-fit-Jeans,LUKA                                                   | 2023-12-31 |
| EC0101-S0G2O0CB        | EC0101-S0G2O0CBBOYC             | Hattric 5-Pocket-Jeans »HATTRIC HARRIS black rinsed 688745 6348.07«      | 2023-12-31 |
| EC0101-S0I0A0OV        | EC0101-S0I0A0OV8U4A             | FORCE Radtrikot »ROSE, Damen Jersey«                                     | 2023-12-31 |
| EC0101-S0K1V0W5        | EC0101-S0K1V0W5KI6Q             | Brandit Cargohose                                                        | 2023-12-20 |
| EC0101-S0N290Q1        | EC0101-S0N290Q1O1A5             | Abakuhaus Duschvorhang »Moderner Digitaldruck mit 12 Haken auf ...       | 2023-12-31 |
| EC0101-S0Z2605Z        | EC0101-S0Z2605ZL4TV             | CALVENDO Puzzle »CALVENDO Puzzle Homeoffice: Endlich am Arbeitsplat« ... | 2023-12-31 |
| EC0601-AKLBB1513460597 | EC0601-3987682551-0-1513460597  | Primaflor-Ideen in Textil Läufer »GIN«, rechteckig, 6 mm Höhe, ...       | 2023-12-06 |
| EC0601-AKLBB1303858774 | EC0601-1032274899-34-1303858774 | Alba Moda Jerseyblazer, mit aufwändiger Verarbeitung                     | 2023-12-31 |



## Lösungsbeispiel
```SQL
-- Solution not valid for SQLite, because of QUALIFY statement
SELECT 
  productID
  , skuID
  , productName
  , loadDate
FROM `it_cs_2023_skufeed`
WHERE productId NOT IN (SELECT productID FROM `it_cs_2023_ignored_products`)
AND loadDate BETWEEN "2023-02-01" AND "2023-02-31"
QUALIFY ROW_NUMBER() OVER(PARTITION BY skuID ORDER BY loadDate DESC) = 1;

-- equal solution


WITH newestSKU AS (
    SELECT
        skuID
         , MAX(loadDate) as loadDate
    FROM it_cs_2023_skufeed
    WHERE loadDate >= "2023-02-01" AND loadDate <= "2023-02-31"
    GROUP BY skuID
)

SELECT sku.skuID
FROM `it_cs_2023_skufeed` AS sku
         JOIN newestSKU AS nsku
              ON nsku.skuId = sku.skuID
                  AND nsku.loadDate = sku.loadDate
WHERE sku.productId NOT IN (SELECT productID FROM `it_cs_2023_ignored_products`)
  AND sku.loadDate >= "2023-02-01" AND sku.loadDate <= "2023-02-31";
```