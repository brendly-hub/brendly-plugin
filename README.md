# Brendly za Claude

Plugin koji Claude-u daje pristup tvojoj Brendly prodavnici: proizvodi i dizajni, izgled izloga,
porudžbine, popusti i isplate. Radi nad istim nalogom kojim se prijavljuješ na
app.brendly.rs, app.brendly.hr, app.brendly.ba ili app.brendly.eu.

Beta je. Ako nešto ne radi ili ti nešto fali, reci to Claude-u u razgovoru: on će to proslediti
Brendly timu zajedno sa onim što je upravo radio.

## Instalacija

U Claude Code-u:

```
/plugin marketplace add brendly-hub/brendly-plugin
/plugin install brendly@brendly
```

Pri prvom korišćenju otvara se pregledač sa Brendly stranicom za odobravanje pristupa. Prijavi se
kao i inače i klikni „Dozvoli". Lozinka ostaje na Brendly platformi i ne prolazi kroz Claude.

Samo server, bez uputstava (radi, ali Claude tada manje zna o tome kako se šta radi):

```
claude mcp add --transport http brendly https://mcp.brendly.rs/mcp
```

## Šta dobijaš

- **Server** `https://mcp.brendly.rs/mcp` sa alatkama za prodavnicu.
- **Uputstva** koja Claude sam koristi kad zatreba: rad sa platformom, katalog i proizvodi,
  teme izloga, brojke i novac.

## Pre nego što kreneš

Sve što Claude uradi odmah se vidi u tvojoj prodavnici; nema probnog režima. Brisanja, objave i
masovne izmene uvek traže tvoju potvrdu pre nego što se izvrše.

## Ažuriranje

```
/plugin marketplace update brendly
```
