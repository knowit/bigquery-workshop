# Workshop: Intro til BigQuery

BigQuery er en datavarehusløsning som kjører på Googles skyplattform (GCP). BigQuery er ‘fully mangaged’, ‘serverless’ og skalerbart, og gir mulighet for analyse av petabytes av data. 
I tillegg tilbyr BigQuery muligheter for maskinlæring med BigQueryML, ‘geospatial’ analyser med BigQuery GIS, og lett tilgjengelige integrasjoner med visualiseringsverktøy som Google Data Studio eller Looker. 
For mer om BigQuery, les her: https://cloud.google.com/bigquery.

I denne workshopen vil vi gi en kort introduksjon til BigQuery før vi frigjør deltakerne til å ta i bruk tjenesten selv gjennom oppgaver designet for å bli kjent med de grunnleggende delene av BigQuery. 
Det vil også være muligheter for å gjøre mer avanserte oppgaver for de som er kjent med BigQuery fra før eller blir ferdig med oppgavene tidlig.

<!--## Komme i gang (Uten å bruke knowit prosjekt)
For å komme i gang med BigQuery er det ikke så mye som kreves:

1. Lag et nytt prosjekt i GCP Console eller bruk et eksisterende. 
(NB: Hvis du har brukt opp alle free credits må det settes opp billing for å utføre queries)
2. Åpne BigQuery fra menyen på venstresiden, eller bruk søkefeltet.

Da vil du havne i BigQuery sin konsoll UI som har all funksjonalitet som kreves for å fullføre oppgavene.
For en mer detaljert gjennomgang og en intro til konsollen se https://cloud.google.com/bigquery/docs/quickstarts/quickstart-cloud-console.
-->
## Komme i gang
For å komme i gang med BigQuery er det ikke så mye som kreves:

1. Logg inn med knowit-brukeren i https://console.cloud.google.com/.
2. Sørg for at du er i prosjektet `fagseminar-bigquery-workshop` under `knowit.no` organisasjonen.
3. Åpne BigQuery fra menyen på venstresiden, eller bruk søkefeltet.

Da vil du havne i BigQuery sin konsoll UI som har all funksjonalitet som kreves for å fullføre oppgavene.
For en mer detaljert gjennomgang og en intro til konsollen se https://cloud.google.com/bigquery/docs/quickstarts/quickstart-cloud-console.

## Oppgaver
### Oppgave 1
I BigQuery organiseres data i tabeller eller views. En tabell eller view tilhører et datasett og et datasett tilhører et GCP prosjekt. 
I denne workshoppen gjør vi det enkelt og samler alt vi gjør i ett datasett:

1. Lag et datasett med datalokasjon i US (samme lokasjon som de offentlige datasettene vi skal ta i bruk).
Se [her](https://cloud.google.com/bigquery/docs/quickstarts/quickstart-cloud-console#create_and_query_a_dataset) for hvordan man lager datasett. 

BigQuery gir tilgang til et stort utvalg av åpent tilgjengelig data gjennom deres Google Cloud Public Dataset Program. 
[Mer om BigQuery public data](https://cloud.google.com/bigquery/public-data). 
I denne workshoppen skal vi ta i bruk ett av disse datasettene:

2. Finn frem det offentlige datasettet `new_york_citibike`: https://console.cloud.google.com/bigquery?p=bigquery-public-data&d=new_york_citibike&page=dataset


Datasettet inneholder 2 tabeller med data fra Citibike i New York: 
- `citibike_stations` inneholder data om sykkelstasjonene til Citibike som er plassert rundt i New York 
- `citibike_trips` inneholder data om alle turer foretatt med Citibike i New York fra 2013 til 2018

Det er sistnevnte som er mest interessant for denne workshoppen, men vi ønsker å ha med oss et felt fra `citibike_stations`; kapasiteten på hver stasjon (`capacity` feltet)

3. Gjør en spørring mot `citibike_stations` for å hente ut kapasiteten til hver stasjon, og lagre resultatet fra spørringen i en tabell i datasettet du lagde i oppg. 1.1

> #### TIPS
> For å lagre tabeller kan man enten bruke BigQuery UIet, eller man kan ta i bruk SQL syntax. 
> [Mer om tabeller](https://cloud.google.com/bigquery/docs/tables#sql)
> ```
> CREATE [OR REPLACE] TABLE mydataset.newtable AS
>   <select query>
> ```

For å begrense datamengden vi skal se på (og ikke bruke opp alle free credits) ønsker vi å avgrense hvor mye data vi bruker fra `citibike_trips` tabellen.

4. Gjør en spørring mot `citibike_trips` for å hente ut kun sykkelturer fra 2017-2018. Ta med alle feltene i tabellene utenom feltene `bikeid` og `customer_plan`. 
Lagre resultatet fra spørringen i en tabell i datsettet ditt.

> #### TIPS
> BigQuery har en oversikt over hvilke funksjoner som kan gjøres på DATETIME felt [her](https://cloud.google.com/bigquery/docs/reference/standard-sql/datetime_functions).

Til slutt ønsker vi å ha info om stasjonenes kapasitet inn sammen med data om sykkelturene:

5. Gjør en spørring mot de 2 nye tabellene i datasettet ditt og lag et view som joiner kapsiteten sammen med de riktige stasjonene i tabellen med data om sykkelturer. 
Pass på at du får med kapasiteten til både start-stasjonen og slutt-stasjonen på turene og gi gode navn til kapasitetene slik at de lett kan skilles f.eks. `start_station_capacity` og `end_station_capacity`. Det kan også være greit å unngå unødvendige dublikatkolonner som kan oppstå når man slår sammen tabeller.

> #### TIPS
> Dersom man skal utføre queries i flere steg (som for eksempel flere joins) kan man i BigQuery bruke `WITH` clause for å lage midlertidige tabeller som brukes under spørringen. 
> [Mer om `WITH` clause her](https://cloud.google.com/bigquery/docs/reference/standard-sql/query-syntax#with_clause)
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
> SELECT *
> FROM temp_table
> JOIN other_table
> ON temp_table.other_id = other_table.id
> ```

Dette viewet kan nå brukes som datagrunnlag for resten av oppgavene.

### Oppgave 2
I denne oppgaven ønsker vi å finne ut av turer som benyttes oftest av citibike-brukerne.

1. Skriv en spørring som finner ut hvilke turer (fra samme startstasjon til stoppstasjon) som tas oftest.
  
> #### TIPS
> En enkel måte å slå sammen to kolonner på er å bruke CONCAT. Eksempel:
> ```
> CONCAT(first_name, ' ', last_name)
> ```
  
### Oppgave 3
I denne oppgaven ønsker vi å se hvordan bruken av Citibike syklene endres i løpet av et døgn.

1. Lag en midlertidig funksjon for å seksjonere timestamps inn i 1 time lange seksjoner, hvor hver seksjon navngives på dette formatet: 'HH:00-HH:00'.
  
> #### TIPS
> I BigQuery har man muligheten til å lage gjenbrukbare (eller midlertidige) [brukerdefinerte funksjoner](https://cloud.google.com/bigquery/docs/reference/standard-sql/user-defined-functions). Eksempel på midlertidig funksjon:
> ```
> CREATE TEMP FUNCTION AddFourAndDivide(x INT64, y INT64)
>   RETURNS FLOAT64
>   AS ((x + 4) / y);
>
> SELECT val, AddFourAndDivide(val, 2)
> FROM UNNEST([2,3,5,8]) AS val;
> ```

2. Skriv en spørring som tar i bruk funksjonen fra 2.1, og finner ut når på døgnet det tas flest turer.


### Oppgave 4

I denne oppgaven er vi interessert i å finne de lengste turene, både i tid og avstand.

1. Skriv en spørring som finner de turene som har lengst reisevei (i luftlinje).
  
> #### TIPS
> BigQuery har 
> [et sett med funksjoner](https://cloud.google.com/bigquery/docs/reference/standard-sql/geography_functions) for å jobbe med geografiske elementer. 
> For at vi skal kunne ta i bruk disse må vi lage geografiske punkt (ST_GEOGPOINT):
> ```
> ST_GEOGPOINT(longitude, latitude)
> ```
  
2. Skriv en spørring som finner gjennomsnittlig, lengste og korteste tid (`tripduration`), samt summen i tid, brukt på turer mellom samme start og stoppested. Sorter på største sum.

### Oppgave 5
I denne oppgaven ønsker vi å se på korrelasjonen mellom brukerattributer og brukeroppførsel.
Datsettet inneholder 3 brukerattributer: 
- `usertype` oppgir om brukeren abonnerer på Citibike (Subscriber) eller betalte for turen direkte (Customer)
- `birth_year` oppgir fødelsåret til brukeren
- `gender` oppgir kjønnet til brukeren. Alternativene her er `female`, `male` eller `unknown`

Det finnes også rader i tabellen hvor disse feltene er blanke. De radene ignorer vi bare.

Velg ut en brukerattributt du ønsker å se nærmere på og ta før deg oppgavene under. 

> #### Tips
> Dersom du ønsker å se på alder; seksjoner brukerne inn i aldersgrupper slik at hver gruppe får en god mengde data. 
> F.eks. kan [CASE](https://cloud.google.com/bigquery/docs/reference/standard-sql/conditional_expressions#case_expr) brukes til dette

1. Skriv en spørring som finner antall trips for hver brukergruppe. 
2. Skriv en spørring som finner hvilke turer (til samme start- og stoppestasjon) som tas oftest for en av brukergruppene. Gjerne utfør spørringen for alle brukergruppene og sammenlign resultater.
3. Skriv en spørring som finner gjennomsnittstiden hver brukergruppene bruker på en tur.

## Ekstraoppgaver
For de som er kjent med BigQuery fra før eller blir ferdig med oppgavene tidlig, 
har vi også laget noen forslag til oppgaver som kan gjøres dersom man har tid igjen:

1. Gjør oppgave 5 på nytt med en av de andre brukerattributene.
2. Utforsk visualisering av resultatene med DataStudio (evt. andre alternativ). 
Velg en oppgave (eller flere) og visualiser resultatet.
3. Utforsk BigQueryML. 
F.eks: Prøv å predikere hvilken tur (fra samme start til endestasjon) som vil bli tatt oftest ila av et døgn, flere døgn frem i tid.
4. Utforsk et annet åpent dataset i BigQuery (https://cloud.google.com/bigquery/public-data)
5. Ta i bruk en av klientbibliotekene til BigQuery (https://cloud.google.com/bigquery/docs/reference/libraries). 
Utfør de samme oppgavene, men via klientbiblioteket.

