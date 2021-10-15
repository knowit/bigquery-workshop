# Intro til BigQuery workshop [DRAFT]

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
1. Lag et datasett hvor dere kan samle alt av tabeller og views dere ønsker å lagre i denne workshoppen
2. Finn frem det offentlige datasettet `new_york_citibike`: https://console.cloud.google.com/bigquery?p=bigquery-public-data&d=new_york_citibike&page=dataset&project=ultra-function-201317


Datasettet inneholder 2 tabeller med data fra Citibike i New York: 
- `citibike_stations` inneholder data om sykkelstasjonene til Citibike som er plassert rundt i New York 
- `citibike_trips` inneholder data om alle tur foretatt med Citibike i New York fra 2013 til 2018

Det er sistnevnte som er mest interessant for denne workshoppen, men vi ønsker å ha med oss et felt fra `citibike_stations`; kapasiteten på hver stasjon (`capacity`feltet)

3. Gjør en spørring mot `citibike_stations` for å hente ut den sist oppdaterte kapasiteten til hver stasjon, og lagre resultatet fra spørringen i en tabell i datasettet du lagde i oppg. 1.1

For å begrense datamengden vi skal se på (og ikke bruke opp alle free credits) ønsker vi å avgrense hvor mye data vi bruker fra `citibike_trips` tabellen.

4. Gjør en spørring mot `citibike_trips` for å hente ut kun sykkelturer fra 2017-2018, og lagre resultatet fra spørringen i en tabell i datsettet ditt.

Til slutt ønsker vi å ha info om stasjonenes kapasitet inn sammen med data om sykkelturene:

5. Gjør en spørring mot de 2 nye tabellene i datasettet ditt og lag et view som joiner kapsiteten sammen med de riktige stasjonene i tabellen med data om sykkelturer.

Dette viewet kan nå brukes som datagrunnlag for resten av oppgavene.

### Oppgave 2
I denne oppgaven ønsker vi å finne ut av hvilke stasjoner som benyttes oftest av citibike brukerne.

1. Finn ut hvilke stasjoner turer oftest startes i, og hvilke de oftest sluttes i.
2. Finn ut hvilke turer (fra samme start stasjon til stopp stasjon) som tas oftest.
3. Finnes det noe korrelasjon mellom stasjonene i oppg. 2.1 og 2.2 og kapasiteten til disse stoppene?

### Oppgave 3

I denne oppgaven er vi interessert i å finne de lengste turene, både i tid og avstand.

1. Finn de turene som bruker lengst tid. Er det noen sammenheng mellom hvilke stasjoner som dras til, eller er det snakk om outliers?
2. Finn de turene som har lengst reisevei (i luftlinje). Er det noen sammenheng mellom hvilke stasjoner som dras til, eller er det snakk om outliers?
3. Vi er interessert i å vite de lengste turene som er tatt hver dag. Lag en scheduled query som presenterer de lengste turene (enten i tid eller lengde) som er tatt det siste døgnet.

### Oppgave 4
I denne oppgaven ønsker vi å se hvordan bruken av citibike endres med tiden
Først ser vi på oppførselen ila et døgn.

1. Lag en gjenbrukbar funksjon for å seksjonere timestamps inn i 1 time lange seksjoner
2. Når på døgnet tas det flest turer?
3. Er det forskjell på hvor brukerne reiser ila døgnet?

Så ser vi på oppførselen ila et år.

4. Lag en gjenbrukbar funksjon for å seksjonere timestamps inn i måneder, hvor output er navnet på måneden og ikke et nummer.
5. Når i året tas det flest turer?
6. Er det forskjell på hvor brukerne reiser ila året?

[Kanskje] Aggregere sammen:

7. Er det forskjell på når på døgnet brukerne tar flest turer avhengig av hvilken tid på året det er?

### Oppgave 5
I denne oppgaven ønsker vi å se på korrelasjonen mellom brukerattributer og brukeroppførsel.
Datsettet inneholder 3 brukerattributer: 
- `usertype` forteller om brukeren abbonnerer på citibike (Subscriber) eller betalte for turen direkte (Customer)
- `birth_year` oppgir fødelsåret til brukeren
- `gender` oppgir kjønnet til brukeren. Alternativene her er `female`, `male` eller `unknown`

Det finnes også rader i tabellen hvor disse feltene er blanke, de ignorer vi bare.

Velg ut en brukerattributt du ønsker å se nærmere på og ta før deg oppgavene under. Tips: Dersom du ønsker å se på alder, seksjoner brukerne inn i aldersgrupper slik at hver gruppe får en god mengde data.

1. Hvordan er balansen av data mellom brukergruppene? Er de representert likt eller er en eller noen grupper representert mer enn andre?
2. Tar de ulike brukergruppene turer til forskjellige stasjoner?
3. Tar de ulike brukergruppene turer på samme eller forskjellige tidspunkt på døgnet?
4. Tar de ulike brukergruppene turer med forskjellig lengde, både i tid og avstand?

## Ekstraoppgaver
For de som er kjent med BigQuery fra før eller blir ferdig med oppgavene tidlig, har vi også laget noen forslag til oppgaver som kan gjøres dersom man har tid igjen:

1. Gjør oppgave 5 på nytt med en av de andre brukerattributene.
2. Utforsk visualisering av resultatene med DataStudio (evt. andre alternativ). Velg en oppgave (eller flere) og visualiser resultatet.
3. Utforsk BigQueryML. F.eks. Prøv å predikere hvilken tur (fra samme start til endestasjon) som vil bli tatt oftest ila av et døgn, flere døgn frem i tid.
4. Utforsk et annet åpent dataset i BigQuery (https://cloud.google.com/bigquery/public-data)
5. Ta i bruk en av klientbibliotekene til BigQuery (https://cloud.google.com/bigquery/docs/reference/libraries). Utfør de samme oppgavene, men via klientbiblioteket.

