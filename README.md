# 2024-11-27 Repetition CRUD

<details open>
<summary>Innehåll</summary>

</details>

### Vad är CRUD

CRUD står helt enkelt för de specifika operationerna man kan göra mot en databas. Sen vilken databas det handlar om är ointressant, man kan fortfarande göra exakt samma operationer i alla databaser men tillvägagångssättet och syntaxen skiljs ju åt.

Det står för

- **C**reate: Skapa ny data i databasen.

- **R**ead: Läs data från databasen, alltså hämta olika typer av data. Det kan vara enskilda entiteter eller en sammanslagning av flera. Då snackar vi om någon from av JOIN

- **U**pdate: Uppdatera befintlig data i databasen.

- **D**elete: Ta bort befintlig data ifrån databasen.

### CRUD i SQL

#### READ

När vi läser data från en SQL-database så använder vi oss av SQL-syntaxen SELECT plus andra nyckelord för att specisera vilken resurs/data vi är ute efter.

```sql
SELECT * FROM users
```

En av de mest grundläggande SELECT-queries, vi hämtar alla rader från en specifik tabell, i det här fallet "users".

Ska vi utöka queryn så vi siktar in oss på specifika users i det här fallet, så får vi lägga till ett "WHERE"-sökning.

```sql
SELECT * FROM users
WHERE user_id = 5
```

Här söker vi specifikt efter usern med id 5. Vi vill fortfarane ha alla kolumner inluderat i vårt svar eftersom vi använder oss av en "\*" innan "FROM"-nyckelordet. Vill vi vara mer specifika så skriver vi in kolumnamnen på det vi söker istället.

```sql
SELECT user_id, first_name, email, username FROM users
WHERE user_id = 43
```

Vill vi ha flera villkor i vår sökning så kan vi använda oss av antingen "AND" eller "OR" för att utöka resultatet. Vi kanske vill inkludera alla som har förnamn "Niklas" eller "Henrik".

```sql
SELECT * FROM users
WHERE first_name = "Niklas" OR first_name = "Henrik"
```

Självklart kan du kombinera "AND" och "OR" på vilket sätt som helst men försök att hålla dina queries så enkla och lättläsliga som möjligt.

Vill man ha en lite mer dynamisk sökning så kan man använda "LIKE" istället. "LIKE" tar emot en sträng som kan vara delar av det du är ute efter. Kanske att en namn ska innehålla en specifik bokstav, eller börja på en specifik bokstav eller börja och sluta på specifka tecken och så vidare. En väldigt dynamisk sökning tillåts med "LIKE"

Till exempel, letar efter alla users vars email slutar på "@jensen.se".

```sql
SELECT * FROM users
WHERE email LIKE "%@jensen.com"
```

Här betyder "%"-tecknet ett wildcard som matchar 0 eller flera karaktärer. Alltså, det ovan kommer att matcha alla dess:

- niklas.f@jensen.com
- niklas@jensen.com
- fahnrich@jensen.com

Vi är alltså ute efter resultat som specifik slutar på "@jensen.com". Men detta "%"-tecken kan placeras var som helst i strängen vilket gör att vi kan göra vår sökning väldigt dynamisk. "LIKE" ignorerar också casings, alltså stor eller liten bokstav spelar ingen roll.

Vi har även "\_"-tecknet som vi kan använda. Det betyder också ett wildcard men den kan bara matcha en karaktär. Vill ha flera wildcards-karaktärer fast ett bestämt antal så får vi använda flera "\_" efter varandra.

### READ med JOIN

Är vi ute efter ett resultat som inkluderar data från en eller flera tabeller så måste vi använda en join för att koppla ihop dessa tabeller. Men vi kan inte koppla ihop tabeller hur som helst utan det måste först finnas en relation mellan tabellerna. Antingen en 1-M-relation eller en M-M-relation. Har vi en 1-M-relation så kan vi använda en simple JOIN men har vi en M-M-relation så behöver vi även en mellanliggande korstabell som vi måste inkludera i vår JOIN-query.

#### JOIN i en 1-M Relation

Låt oss utgå ifrån chinook-databsen och hämta hem alla customers med deras respektive invoices.

```sql
SELECT customers.CustomerId, FirstName, LastName, InvoiceId, Total
FROM customers
JOIN invoices ON customers.CustomerId = invoices.CustomerId
```

Här fick vi det förväntade resultatet, vi fick en lista på alla invoices som varje customer hade kopplat till sig. Vi kan till exmpel se att customer med CustomerId 2 _( Leonie Köhler )_ har 7 stycken invoices kopplat till sig på olika summor. Vill man få ut en lista på varje cusomters totala summa för alla dess invoices så skulle man kunna använda sig av "SUM" och "GROUP BY" för att få ut ett sånt resultat men det får ni experimentera med själv.

#### JOIN i en M-M Relation

Låt oss ta exempelet med "tracks" och "playlists", en track kan finnas på flera playlists, och en playlist kan innehålla flera tracks så där har vi en M-M-relation. Denna relation representeras utav en korstabell så för att få ut det här resultatet så måste vi inkludera korstabellen i våran JOIN.

```sql
SELECT playlists.PlayListId, playlists.Name, TrackId, tracks.Name
FROM playlists
JOIN playlist_track ON playlist_track.PlayListId = playlists.PlayListid
JOIN tracks ON tracks.TrackId = playlist_track.TrackId
```

Så denna query funkar fint, vi får det vi är ute efter men vi kanske kan snygga till queryn lite. Det kan vi göra genom att ge temporära namn till våra tabeller och kolumner. Till exemepel "Name" kommer existera i två av tabellerna så i resultatet kommer de att namnges "Name" och "Name:1" och det säger inte så mycket. Låt oss använda nyckelordet "AS" för att fixa till detta.

```sql
SELECT p.PlayListId, p.Name AS playListName, TrackId, t.Name AS trackName
FROM playlists AS p
JOIN playlist_track AS pt ON pt.PlayListId = p.PlayListid
JOIN tracks AS t ON t.TrackId = pt.TrackId
```

Så nu har vi givit temporära namn till det som vi tycker behöver det och det ser bättre ut. Om det här resultatet skulle skickas till en server som i sin tur skickar det vidare till en klient så kommer kolumnnamnet vara det som vi har döpt de till.
