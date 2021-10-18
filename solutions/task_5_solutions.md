# Oppgave 5

### Med `birth_year` som brukerattributt
1. Hvordan er balansen av data mellom brukergruppene? Er de representert likt eller er en eller noen grupper representert mer enn andre?
```
CREATE TEMP FUNCTION GetAgeGroup(birth_year INT64) RETURNS STRING AS (
   CASE
       WHEN birth_year > 2003 THEN '0-18'
       WHEN birth_year > 1991 THEN '19-30'
       WHEN birth_year > 1977 THEN '31-44'
       WHEN birth_year > 1954 THEN '45-67'
       WHEN birth_year > 1900 THEN '67+'
       ELSE 'unknown'
   END
);
 
SELECT GetAgeGroup(birth_year) AS age_group, count (*) as num_trips
FROM `<dataset_name>.<view_name>`
GROUP BY age_group
ORDER BY num_trips DESC
```
 
2. Tar de ulike brukergruppene turer til forskjellige stasjoner?
```
CREATE TEMP FUNCTION GetAgeGroup(birth_year INT64) RETURNS STRING AS (
   CASE
       WHEN birth_year > 2003 THEN '0-18'
       WHEN birth_year > 1991 THEN '19-30'
       WHEN birth_year > 1977 THEN '31-44'
       WHEN birth_year > 1954 THEN '45-67'
       WHEN birth_year > 1900 THEN '67+'
       ELSE 'unknown'
   END
);

WITH t1 AS (
   SELECT
   GetAgeGroup(birth_year) as age_group,
   start_station_name,
   end_station_name,
   FROM `<dataset_name>.<view_name>`
   WHERE GetAgeGroup(birth_year) = '31-44' [REPLACE with age_group to analyse]
)
 
SELECT
   age_group,
   start_station_name,
   end_station_name,
   count (*) as num_trips
FROM t1
GROUP BY start_station_name, end_station_name, age_group
ORDER BY num_trips DESC
```

3. Tar de ulike brukergruppene turer på samme eller forskjellige tidspunkt på døgnet?
```
CREATE TEMP FUNCTION GetAgeGroup(birth_year INT64) RETURNS STRING AS (
   CASE
       WHEN birth_year > 2003 THEN '0-18'
       WHEN birth_year > 1991 THEN '19-30'
       WHEN birth_year > 1977 THEN '31-44'
       WHEN birth_year > 1954 THEN '45-67'
       WHEN birth_year > 1900 THEN '67+'
       ELSE 'unknown'
   END
);

WITH t1 AS (
   SELECT
       GetAgeGroup(birth_year) AS age_group,
       starttime,
   FROM `<dataset_name>.<view_name>`
   WHERE GetAgeGroup(birth_year) = '31-44' [REPLACE with group to analyse]
)
 
SELECT
   EXTRACT(HOUR FROM starttime) AS hour,
   age_group,
   count(*) as num_trips
FROM t1
GROUP BY hour, age_group
ORDER BY num_trips DESC
```

4. Finn gjennomsnittstiden hver brukergruppene bruker på en tur.
```
CREATE TEMP FUNCTION GetAgeGroup(birth_year INT64) RETURNS STRING AS (
   CASE
       WHEN birth_year > 2003 THEN '0-18'
       WHEN birth_year > 1991 THEN '19-30'
       WHEN birth_year > 1977 THEN '31-44'
       WHEN birth_year > 1954 THEN '45-67'
       WHEN birth_year > 1900 THEN '67+'
       ELSE 'unknown'
   END
);

with t1 AS (
   SELECT
       GetAgeGroup(birth_year) AS age_group,
       tripduration
   FROM `<dataset_name>.<view_name>`
)
 
SELECT age_group, AVG(tripduration) AS mean_duration
FROM t1
GROUP BY age_group
```

### Med `usertype` som brukerattributt
1. Hvordan er balansen av data mellom brukergruppene? Er de representert likt eller er en eller noen grupper representert mer enn andre?
```
SELECT usertype, count (*) as num_trips
FROM `<dataset_name>.<view_name>`
GROUP BY usertype
ORDER BY num_trips DESC
```

2. Tar de ulike brukergruppene turer til forskjellige stasjoner?
```
SELECT
   usertype,
   start_station_name,
   end_station_name,
   count (*) as num_trips
FROM `<dataset_name>.<view_name>`
WHERE usertype = 'Subscriber' [REPLACE with group to analyse]
GROUP BY start_station_name, end_station_name, usertype
ORDER BY num_trips DESC
```

3. Tar de ulike brukergruppene turer på samme eller forskjellige tidspunkt på døgnet?
```
SELECT
   EXTRACT(HOUR FROM starttime) AS hour,
   usertype,
   count(*) as num_trips
FROM `<dataset_name>.<view_name>`
WHERE usertype = 'Subscriber' [REPLACE with group to analyse]
GROUP BY hour, usertype
ORDER BY num_trips DESC
```

4. Finn gjennomsnittstiden hver brukergruppene bruker på en tur.
```
SELECT usertype, AVG(tripduration) AS mean_duration
FROM `<dataset_name>.<view_name>`
GROUP BY usertype
```

### Med `gender` som brukerattributt
1. Hvordan er balansen av data mellom brukergruppene? Er de representert likt eller er en eller noen grupper representert mer enn andre?
```
SELECT gender, count (*) as num_trips
FROM `<dataset_name>.<view_name>`
GROUP BY gender
ORDER BY num_trips DESC
```

2. Tar de ulike brukergruppene turer til forskjellige stasjoner?
```
SELECT
   gender,
   start_station_name,
   end_station_name,
   count (*) as num_trips
FROM `<dataset_name>.<view_name>`
WHERE gender = 'female' [REPLACE with group to analyse]
GROUP BY start_station_name, end_station_name, gender
ORDER BY num_trips DESC
```

3. Tar de ulike brukergruppene turer på samme eller forskjellige tidspunkt på døgnet?
```
SELECT
   EXTRACT(HOUR FROM starttime) AS hour,
   gender,
   count(*) as num_trips
FROM `<dataset_name>.<view_name>`
WHERE gender = 'female' [REPLACE with group to analyse]
GROUP BY hour, gender
ORDER BY num_trips DESC
```

4. Finn gjennomsnittstiden hver brukergruppene bruker på en tur.
```
SELECT gender, AVG(tripduration) AS mean_duration
FROM `<dataset_name>.<view_name>`
GROUP BY gender
```
