# Brendly: povezivanje

Ovo je prvi korak, pre svakog drugog posla. Bez veze sa nalogom nijedna `brendly_*` alatka ne radi.

**Lozinku nikad ne tražim i nikad ne primam.** Prijava se radi na Brendly stranici, u pregledaču
korisnika. Ako mi korisnik sam pošalje lozinku ili token, ne koristim ih i kažem mu da to nije
potrebno.

## Prvo proveri, pa onda objašnjavaj

Ne pričam korisniku o podešavanju dok ne znam u kakvom je stanju. Redom:

1. Pozovi `brendly_context action:"whoami"`.
2. **Prošlo je** - veza radi. Reci mu nalog (mejl) i pređi na posao: `list_shops`, pa
   `select_shop`. Ne spominji podešavanje.
3. **Alatka ne postoji u ovoj sesiji** - server nije povezan. Idi na "Kako se povezuje".
4. **Vratila je grešku pristupa** (401, `unauthorized`, `invalid_token`, poruka o prijavi) -
   veza postoji ali odobrenje je isteklo. Idi na "Kad traži prijavu ponovo".

Nikad ne javljam "ne mogu da pristupim Brendly-ju" bez ovog poziva: prazna lista prodavnica i
nula porudžbina su tačno stanje, ne greška veze.

## Kako se povezuje

Prvo ustanovi gde korisnik radi, jer se koraci razlikuju. Ako ne znaš, pitaj ga jednom rečenicom:
"Koristiš Claude aplikaciju ili Claude Code?"

### Claude Code (kartica Code u aplikaciji, ili terminal)

Plugin nosi i vezu i uputstva. Ako je plugin instaliran a alatki nema, treba još samo odobrenje:

1. Reci mu da u polje za poruku unese `/mcp`.
2. Iz liste izabere **brendly**, pa **Authenticate**.
3. Otvara se Brendly stranica: prijavi se kao i inače i klikne **Dozvoli**.

Ako plugin nije instaliran:

```text
/plugin marketplace add brendly-hub/brendly-plugin
/plugin install brendly@brendly
```

Pa onda `/mcp` i odobrenje, kao gore.

### Claude aplikacija (Desktop ili claude.ai)

1. **Settings**, pa **Connectors**.
2. **Add custom connector**.
3. Ime `Brendly`, adresa `https://mcp.brendly.rs/mcp`.
4. **Add**, pa **Connect**, pa odobrenje na Brendly stranici.
5. U novom razgovoru, dugme **+** pa **Connectors**: proveri da je Brendly uključen.

Na Team i Enterprise nalozima konektor dodaje administrator organizacije. Ako korisnik nema
**Add custom connector**, to je razlog: reci mu da se obrati administratoru, ne šalji ga da traži
dalje po podešavanjima.

### Posle odobrenja

Odmah pozovi `whoami` i potvrdi ishod. **Ne verujem tome što je korisnik rekao da je kliknuo:**
potvrda je to što alatka vrati nalog. Kad prođe, kaži mu mejl naloga i koliko prodavnica vidiš,
pa predloži prvi korak.

## Kad traži prijavu ponovo

Jedna prijava pokriva ceo nalog i sva tržišta (RS, HR, BA, EU) i **ne treba je ponavljati u
svakom razgovoru**. Ako korisnik kaže da mora svaki put, to nije normalno i nije njegova greška.

| Šta je | Kako se prepozna | Šta mu reci |
|---|---|---|
| Odobrenje isteklo od neaktivnosti | Nije radio sa Brendly-jem duže od dve nedelje | Jedno ponovno odobrenje i dalje radi normalno |
| Konektor isključen u razgovoru | Alatki nema, a konektor postoji u Settings | Dugme **+**, pa **Connectors**, uključi Brendly |
| Traži prijavu u svakom novom razgovoru | Radi, pa posle kratke pauze opet `/mcp` | To je greška na našoj strani: ponudi `brendly_feedback` |

Za poslednji slučaj pošalji povratnu informaciju kroz `brendly_feedback`, korisnikovim rečima, i
navedi da odobrenje ne opstaje između sesija. Ne izmišljam objašnjenje zašto se to dešava.

## Tuđi nalog

Ako `whoami` vrati mejl koji nije korisnikov, **ništa ne menjam**. Kažem mu koji je nalog
povezan i da se na `app.brendly.rs` (ili `.hr`, `.ba`, `.eu`) odjavi, prijavi svojim nalogom i
ponovo poveže Brendly. Čitanje tuđe prodavnice takođe ne radim.

## Šta posle veze

Uputstva za sam posao su u zasebnim skillovima: `brendly-mcp` (jezgro, izbor prodavnice,
destruktivne akcije), `brendly-katalog` (proizvodi i dizajni), `brendly-teme` (izlog),
`brendly-novac` (brojke i isplate). Ako ih nemam kao skillove, ista uputstva čitam sa servera
preko `brendly_uputstva`.
