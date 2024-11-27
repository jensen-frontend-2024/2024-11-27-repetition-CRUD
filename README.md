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
