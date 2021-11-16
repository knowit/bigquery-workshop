# Oppgave 3
1. Lag en midlertidig funksjon for å seksjonere timestamps inn i 1 time lange seksjoner, hvor hver seksjon navngives på dette formatet: 'HH:00-HH:00'.
```
CREATE TEMP FUNCTION GetHourSectionFromDatetime(d DATETIME) AS
(
   CONCAT(EXTRACT(HOUR from d), ':00-', EXTRACT(HOUR from d)+1, ':00')
);
```

2. Skriv en spørring som tar i bruk funksjonen fra 2.1, og finner ut når på døgnet det tas flest turer.
```
CREATE TEMP FUNCTION GetHourSectionFromDatetime(d DATETIME) AS
(
   CONCAT(EXTRACT(HOUR from d), ':00-', EXTRACT(HOUR from d)+1, ':00')
);
 
SELECT GetHourSectionFromDatetime(starttime) AS section, count(*) as num_trips
FROM `<dataset_name>.<view_name>`
GROUP BY section
ORDER BY num_trips DESC
```
