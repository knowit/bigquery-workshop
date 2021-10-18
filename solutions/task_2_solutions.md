# Oppgave 2
1. Finn ut hvilke stasjoner turer oftest startes i, og hvilke de oftest sluttes i, og sammenlign dette med hvilke stasjoner som har høyest kapasitet.

Fra start-stasjon:
```
SELECT start_station_id, start_station_name, start_station_capacity, count(*) AS num_trips
FROM `<dataset_name>.<view_name>`
GROUP BY start_station_id, start_station_name, start_station_capacity
ORDER BY num_trips DESC
```

Fra slutt-stasjon:
```
SELECT end_station_id, end_station_name, end_station_capacity, count(*) AS num_trips
FROM `<dataset_name>.<view_name>`
GROUP BY end_station_id, end_station_name, end_station_capacity
ORDER BY num_trips DESC
```

2. Finn ut hvilke turer (fra samme start stasjon til stopp stasjon) som tas oftest.
```
SELECT CONCAT(start_station_name, ' - ', end_station_name) AS trip_stations, count(*) AS num_trips
FROM `<dataset_name>.<view_name>`
GROUP BY trip_stations
ORDER BY num_trips DESC
```
