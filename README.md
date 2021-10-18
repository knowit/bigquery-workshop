# Intro til BigQuery workshop

BigQuery er en datavarehusløsning som kjører på Googles skyplattform (GCP). BigQuery er ‘fully mangaged’, ‘serverless’ og skalerbart, og gir mulighet for analyse av petabytes av data. I tillegg tilbyr BigQuery muligheter for maskinlæring med BigQueryML, ‘geospatial’ analyser med BigQuery GIS, og lett tilgjengelige integrasjoner med visualiseringsverktøy som Google Data Studio eller Looker. For mer om BigQuery, les her: https://cloud.google.com/bigquery.

I denne workshopen vil vi gi en kort introduksjon til BigQuery før vi frigjør deltakerne til å ta i bruk tjenesten selv gjennom oppgaver designet for å bli kjent med de grunnleggende delene av BigQuery. Det vil også være muligheter for å gjøre mer avanserte oppgaver for de som er kjent med BigQuery fra før eller blir ferdig med oppgavene tidlig.

## Komme i gang
For å komme i gang med BigQuery er det ikke så mye som kreves:

1. Lag et nytt prosjekt i GCP Console eller bruk et eksisterende. (NB: Hvis du har brukt opp alle free credits må det settes opp billing for å utføre queries)
2. Åpne BigQuery fra menyen på venstresiden, eller bruk søkefeltet.

Da vil du havne i BigQuery sin konsoll UI som har all funksjonalitet som kreves for å fullføre oppgavene.
For en mer detaljert gjennomgang og en intro til konsollen se https://cloud.google.com/bigquery/docs/quickstarts/quickstart-cloud-console.

## Oppgaver
### Oppgave 1
I BigQuery organiseres alt av tabeller, views, m.m. inn i datasett. I denne workshoppen gjør vi det enklelt og samler alt vi gjør i et datasett:
1. Lag et datasett. Se [her](https://cloud.google.com/bigquery/docs/quickstarts/quickstart-cloud-console#create_and_query_a_dataset) for hvordan man lager datasett. 

BigQuery gir tilgang til et stort utvalg av åpent tilgjengelig data gjennom deres Google Cloud Public Dataset Program. [Mer om BigQuery public data](https://cloud.google.com/bigquery/public-data). I denne workshoppen skal vi ta i bruk en av disse datasettene:

2. Finn frem det offentlige datasettet `new_york_citibike`: https://console.cloud.google.com/bigquery?p=bigquery-public-data&d=new_york_citibike&page=dataset


Datasettet inneholder 2 tabeller med data fra Citibike i New York: 
- `citibike_stations` inneholder data om sykkelstasjonene til Citibike som er plassert rundt i New York 
- `citibike_trips` inneholder data om alle turer foretatt med Citibike i New York fra 2013 til 2018

Det er sistnevnte som er mest interessant for denne workshoppen, men vi ønsker å ha med oss et felt fra `citibike_stations`; kapasiteten på hver stasjon (`capacity` feltet)

3. Gjør en spørring mot `citibike_stations` for å hente ut kapasiteten til hver stasjon, og lagre resultatet fra spørringen i en tabell i datasettet du lagde i oppg. 1.1

> #### TIPS
> For å lagre tabeller kan man enten bruke BigQuery UIet, eller man kan ta i bruk SQL syntax. [Mer om tabeller](https://cloud.google.com/bigquery/docs/tables#sql)
> ```
> CREATE [OR REPLACE] TABLE mydataset.newtable AS
>   <select query>
> ```

For å begrense datamengden vi skal se på (og ikke bruke opp alle free credits) ønsker vi å avgrense hvor mye data vi bruker fra `citibike_trips` tabellen.

4. Gjør en spørring mot `citibike_trips` for å hente ut kun sykkelturer fra 2017-2018 uten feltene `bikeid` og `customer_plan`. Lagre resultatet fra spørringen i en tabell i datsettet ditt.

> #### TIPS
> BigQuery har en oversikt over hvilke funksjoner som kan gjøres på DATETIME felt [her](https://cloud.google.com/bigquery/docs/reference/standard-sql/datetime_functions).

Til slutt ønsker vi å ha info om stasjonenes kapasitet inn sammen med data om sykkelturene:

5. Gjør en spørring mot de 2 nye tabellene i datasettet ditt og lag et view som joiner kapsiteten sammen med de riktige stasjonene i tabellen med data om sykkelturer. Pass på at du får med kapasiteten til både start-stasjonen og slutt-stasjonen på turene.

> #### TIPS
> Dersom man skal utføre queries i flere steg (som for eksempel flere joins) kan man i BigQuery bruke `WITH` clause for å lage midlertidige tabeller som brukes under spørringen. [Mer om `WITH` clause her](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax#with_clause)
>
> Eksempel:
> ```
> WITH temp_table AS (
>    SELECT *
>    FROM this_table
>    JOIN other_table
>    ON this_table.id = other_table.id
>  )
>  
>  SELECT *
>  FROM temp_table
>  JOIN other_table
>  ON temp_table.other_id = other_table.id
> ```

Dette viewet kan nå brukes som datagrunnlag for resten av oppgavene.

### Oppgave 2
I denne oppgaven ønsker vi å finne ut av hvilke stasjoner og turer som benyttes oftest av citibike brukerne.

1. Finn ut hvilke stasjoner turer oftest startes i, og hvilke de oftest sluttes i, og sammenlign dette med hvilke stasjoner som har høyest kapasitet.
2. Finn ut hvilke turer (fra samme start stasjon til stopp stasjon) som tas oftest.
  
> #### TIPS
> En enkel måte å slå sammen to kolonner på er å bruke CONCAT. Eksempel:
> ```
> CONCAT(first_name, ' ', last_name)
> ```
  
### Oppgave 3
I denne oppgaven ønsker vi å se hvordan bruken av Citibike syklene endres i løpet av et døgn.

1. Lag en midlertidig funksjon for å seksjonere timestamps inn i 1 time lange seksjoner, hvor hver seksjon navngives på dette formatet: 'HH:00-HH:00'.
  
> #### TIPS
> I BigQuery har man muligheten til å lage gjenbrukbare (eller midlertidige) [brukerdefinerte funksjoner](https://cloud.google.com/bigquery/docs/reference/standard-sql/user-defined-functions). Eksempel på persitent funksjon:
> ```
> CREATE FUNCTION mydataset.AddFourAndDivide(x INT64, y INT64)
>   RETURNS FLOAT64
>   AS ((x + 4) / y);
> ```

2. Når på døgnet tas det flest turer?


### Oppgave 4

I denne oppgaven er vi interessert i å finne de lengste turene, både i tid og avstand.

1. Finn de turene som bruker lengst tid, og sammenlign dette med hvor lang tid det gjennomsnittlig er brukt på turer med samme start og slutt stasjon.
2. Finn de turene som har lengst reisevei (i luftlinje).
  
> #### TIPS
> BigQuery har [et sett med funksjoner](https://cloud.google.com/bigquery/docs/reference/standard-sql/geography_functions) for å jobbe med geografiske elementer. For at vi skal kunne ta i bruk disse må vi lage geografiske punkt (ST_GEOGPOINT):
> ```
> ST_GEOGPOINT(longitude, latitude)
> ```

### Oppgave 5
I denne oppgaven ønsker vi å se på korrelasjonen mellom brukerattributer og brukeroppførsel.
Datsettet inneholder 3 brukerattributer: 
- `usertype` oppgir om brukeren abbonnerer på Citibike (Subscriber) eller betalte for turen direkte (Customer)
- `birth_year` oppgir fødelsåret til brukeren
- `gender` oppgir kjønnet til brukeren. Alternativene her er `female`, `male` eller `unknown`

Det finnes også rader i tabellen hvor disse feltene er blanke. De radene ignorer vi bare.

Velg ut en brukerattributt du ønsker å se nærmere på og ta før deg oppgavene under. 

> #### Tips
> Dersom du ønsker å se på alder, seksjoner brukerne inn i aldersgrupper slik at hver gruppe får en god mengde data. F.eks. kan [CASE](https://cloud.google.com/bigquery/docs/reference/standard-sql/conditional_expressions#case_expr) brukes til dette

1. Hvordan er balansen av data mellom brukergruppene? Er de representert likt eller er en eller noen grupper representert mer enn andre?
2. Tar de ulike brukergruppene turer til forskjellige stasjoner?
3. Tar de ulike brukergruppene turer på samme eller forskjellige tidspunkt på døgnet?
4. Finn gjennomsnittstiden hver brukergruppene bruker på en tur.

## Ekstraoppgaver
For de som er kjent med BigQuery fra før eller blir ferdig med oppgavene tidlig, har vi også laget noen forslag til oppgaver som kan gjøres dersom man har tid igjen:

1. Gjør oppgave 5 på nytt med en av de andre brukerattributene.
2. Utforsk visualisering av resultatene med DataStudio (evt. andre alternativ). Velg en oppgave (eller flere) og visualiser resultatet.
3. Utforsk BigQueryML. F.eks: Prøv å predikere hvilken tur (fra samme start til endestasjon) som vil bli tatt oftest ila av et døgn, flere døgn frem i tid.
4. Utforsk et annet åpent dataset i BigQuery (https://cloud.google.com/bigquery/public-data)
5. Ta i bruk en av klientbibliotekene til BigQuery (https://cloud.google.com/bigquery/docs/reference/libraries). Utfør de samme oppgavene, men via klientbiblioteket.

