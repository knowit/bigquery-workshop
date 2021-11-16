# Oppgave 2
1. Skriv en spørring som finner ut hvilke turer (fra samme startstasjon til stoppstasjon) som tas oftest.
```
SELECT CONCAT(start_station_name, ' - ', end_station_name) AS trip_stations, count(*) AS num_trips
FROM `<dataset_name>.<view_name>`
GROUP BY trip_stations
ORDER BY num_trips DESC
```
