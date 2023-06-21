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

