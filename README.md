# Brendly za Claude

**Vodi svoju Brendly prodavnicu razgovorom.** Kažeš Claude-u šta želiš, a on to uradi u tvojoj
prodavnici: napravi proizvode od dizajna, sredi boje i cene, napiše opise, uredi izlog i pročita
brojke. Svaki korak ti potvrdi, a sve veće izmene tek uz tvoje odobrenje.

```text
„Evo 20 dizajna iz foldera, napravi po jednu majicu za svaki i objavi ih."
„Dodaj Crnu i Teget na sve majice i podigni maržu na 900 dinara."
„Napravi isti proizvod kao duks, dizajn neka stoji tačno kao na majici."
„Svakom proizvodu napiši svoj opis i SEO naslov, u istom tonu."
„Stavi logo kod srca na svih 15 majica odjednom."
```

> **Beta.** Plugin je u javnom testiranju. Ako nešto ne radi kako očekuješ ili ti nešto fali,
> reci to Claude-u u razgovoru: on će to proslediti Brendly timu, zajedno sa onim što je upravo
> radio.

---

## Sadržaj

- [Šta dobijaš](#šta-dobijaš)
- [Instalacija](#instalacija)
- [Prvi razgovor](#prvi-razgovor)
- [Mogućnosti](#mogućnosti)
- [Kako Claude radi sa tvojom prodavnicom](#kako-claude-radi-sa-tvojom-prodavnicom)
- [Šta plugin ne radi](#šta-plugin-ne-radi)
- [Povratna informacija](#povratna-informacija)
- [Ažuriranje i uklanjanje](#ažuriranje-i-uklanjanje)
- [Česta pitanja](#česta-pitanja)

---

## Šta dobijaš

| Deo | Šta radi |
|---|---|
| **Brendly server** | Veza sa tvojim Brendly nalogom preko `https://mcp.brendly.rs/mcp`. Daje Claude-u iste mogućnosti koje imaš u Brendly platformi. |
| **Uputstva** | Četiri skupa znanja koje Claude sam koristi kad zatreba: rad sa platformom, katalog i proizvodi, izgled izloga, brojke i novac. Zahvaljujući njima zna kako se šta radi na Brendly-ju, bez objašnjavanja sa tvoje strane. |

## Instalacija

**Potrebno:** [Claude Code](https://claude.com/claude-code) i Brendly nalog.

U Claude Code-u pokreni:

```text
/plugin marketplace add brendly-hub/brendly-plugin
/plugin install brendly@brendly
```

Pri prvom korišćenju Claude otvara pregledač sa Brendly stranicom za odobravanje pristupa.
Prijaviš se na **app.brendly.rs** kao i inače i klikneš **Dozvoli**. Lozinka ostaje na Brendly
platformi i nikad ne prolazi kroz Claude.

Jedna prijava pokriva ceo nalog: sve tvoje prodavnice, na svim tržištima (Srbija, Hrvatska,
Bosna i Hercegovina i EU), dostupne su odmah. Claude ih izlista i radi u onoj koju izabereš,
isto kao što tržište menjaš u samoj platformi.

<details>
<summary>Samo server, bez uputstava</summary>

Server može da se doda i sam. Radi, ali Claude tada manje zna o tome kako Brendly funkcioniše,
pa češće pita i sporije dolazi do rešenja.

```text
claude mcp add --transport http brendly https://mcp.brendly.rs/mcp
```

</details>

## Prvi razgovor

Dobar početak je da Claude upozna tvoj nalog:

```text
„Koje prodavnice imam i šta je u njima?"
„Da li mi je prodavnica spremna da prima porudžbine? Šta fali?"
„Pokaži mi šta imam u katalogu i koji proizvodi nisu objavljeni."
```

Posle svakog posla Claude ti kaže šta je uradio, šta je bilo pre a šta je sada, gde to možeš da
vidiš i šta, ako išta, nije uspelo.

---

## Mogućnosti

### Proizvodi i dizajni

Kreiranje proizvoda od dizajna, pojedinačno ili u seriji do 200 odjednom, i izmena postojećih:
nazivi, opisi, SEO, boje, kategorije, marže, objava.

**Kreiranje**

```text
„Evo ti dizajn, napravi mušku majicu s njim."
„Napravi proizvod od dizajna koji je već u mojoj galeriji."
„Napravi isti proizvod u svim bojama koje tip nudi."
„Napravi majicu i odmah je objavi, sa opisom i SEO poljima."
„Napravi mi kolekciju za Novu godinu, 10 proizvoda, dizajne smisli sam."
```

**Izmena postojećih**

```text
„Prekrsti proizvod u Tesla 1856, napiši opis i SEO i objavi ga."
„Svakom od 15 proizvoda napiši svoj meta naslov, po imenu i godini."
„Izbaci Žutu sa svih proizvoda, ne prodaje se."
„Popuni meta opis samo tamo gde ga nema, ostale ne diraj."
„Skloni sve cegere sa izloga."
```

**Raspored dizajna**

```text
„Razvuci dizajn maksimalno i podigni ga uz vrat."
„Smanji dizajn na logo kod srca, na svim majicama."
„Pomeri dizajn malo niže i nakrivi ga za 15 stepeni."
„Zameni dizajn na ovom proizvodu novom slikom."
„Uzmi sve organic majice i napravi iste proizvode kao duksove."
```

Kod prenosa dizajna na drugi tip proizvoda Claude čuva ono što je važno: na sličnom okviru isti
raspored, na mali logo istu stvarnu veličinu, a na potpuno drugom okviru (šolja, ceger) prilagodi
dizajn i to ti kaže.

**Cene, marže i slike**

```text
„Pokaži mi trenutne cene svih majica."
„Postavi maržu po tipu: majice 800, duksovi 1200, cegeri 500."
„Spusti sve cene za 10%."
„Sakrij mokap leđa, neka se u izlogu vidi samo prednja strana."
„Postavi moju fotografiju kao prvu sliku za Belu."
```

### Izgled izloga

Boje, pismo, zaglavlje, futer i sekcije početne strane, uz nacrt koji možeš da pregledaš pre
objave i snimak pre svake izmene.

```text
„Uskladi boje prodavnice sa mojim logom."
„Stavi logo u zaglavlje i dodaj link ka Instagramu u futer."
„Dodaj sekciju sa istaknutim proizvodima i stavi u nju pet najprodavanijih."
„Napiši tekst za naslovnu sekciju, u duhu kolekcije."
„Na telefonu neka idu dva proizvoda po redu."
„Napravi izmene, ali nemoj još da objavljuješ."
„Prekopiraj temu sa srpske prodavnice na hrvatsku."
```

### Kategorije

```text
„Napravi tri kategorije po epohama i rasporedi proizvode."
„Prebaci sve proizvode iz jedne kategorije u drugu, pa obriši staru."
„Napravi kategoriju Novo i stavi u nju sve što je napravljeno ovog meseca."
„Sakrij kategoriju iz navigacije, ali je ne briši."
```

### Galerija dizajna

```text
„Ubaci ove slike u galeriju."
„Napravi folder Letopis i prebaci u njega sve dizajne kolekcije."
„Nađi mi onaj dizajn sa Teslom."
„Koliko mesta zauzimam u galeriji?"
```

### Popusti i promo kodovi

Popusti sa početkom i krajem, promo kodovi i serije kodova, uz računicu marže pre nego što
išta upiše.

```text
„Napravi popust 20% na celu kategoriju, traje do nedelje."
„Napravi promo kod LETO20 za 20% popusta."
„Napravi deset jedinstvenih kodova za saradnike."
„Koji proizvodi mogu da podnesu 40% popusta, a da i dalje zarađujem?"
„Pripremi prodavnicu za Crni petak: popusti, tema, istaknuti proizvodi."
„Da li mi neki popust ističe ovih dana?"
```

### Porudžbine, kupci i reklamacije

Pregled i pretraga. Porudžbine se ne menjaju kroz Claude, isto kao u platformi.

```text
„Pokaži mi poslednjih deset porudžbina."
„Koje porudžbine su otkazane i zašto?"
„Gde je sad ta pošiljka kod kurira?"
„Ko mi je naručio više od tri puta?"
„Koji je najčešći razlog povrata?"
```

### Prodaja i zarada

```text
„Kako ide ovaj mesec u odnosu na prošli?"
„Daj mi deset najprodavanijih za poslednja tri meseca."
„Koja boja i koja veličina se najbolje prodaju?"
„Koji proizvod mi nosi najviše zarade, ne prometa?"
„Napravi izveštaj o prošlom mesecu koji mogu da pošaljem knjigovođi."
„Skloni sa izloga sve što nije prodalo ništa za pola godine."
```

### Isplate

Stanje za isplatu, istorija i zahtev za isplatu. Kod isplate na račun firme Claude ti kaže tačno
šta faktura mora da sadrži i priloži je kad mu je pošalješ.

```text
„Koliko para mogu da podignem?"
„Podigni mi novac na Native Teams."
„Podigni mi novac na račun firme." (Claude traži fakturu i objašnjava šta mora da sadrži)
„Pokaži mi sve moje zahteve za isplatu."
```

### Podešavanja prodavnice i naloga

```text
„Napiši opis prodavnice i postavi SEO naslov i opis."
„Postavi ovu sliku kao logo."
„Dodaj engleski kao drugi jezik."
„Stavi obaveštenje na sve stranice: dostava kasni zbog praznika."
„Postavi mi Facebook piksel."
„Napravi istu prodavnicu na hrvatskom tržištu."
```

---

## Kako Claude radi sa tvojom prodavnicom

- **Sve je uživo.** Izmene se odmah vide u tvojoj prodavnici; ne postoji probni režim.
- **Veće izmene traže tvoju potvrdu.** Brisanje, objava teme, masovne izmene i isplata se ne
  izvršavaju dok ih izričito ne odobriš. Claude ti pre toga kaže šta će se tačno desiti.
- **Nijedan upis naslepo.** Pre izmene Claude pročita trenutno stanje, upiše samo ono što si
  tražio i posle proveri da li je upisano. Ako se promeni nešto što nisi tražio, kaže ti.
- **Pre i posle.** Za svaku izmenu dobijaš staro i novo stanje, pa znaš tačno šta se promenilo.
- **Tema ima rezervnu kopiju.** Pre svake izmene izgleda pravi se snimak, pa se uvek možeš vratiti.
- **Isti pristup kao u platformi.** Claude vidi i menja tačno ono što i ti možeš u svom nalogu,
  ništa više.

## Šta plugin ne radi

- **Plaćanja i podaci za naplatu:** unos kartice, uključivanje načina plaćanja, povezivanje
  Stripe-a i PayPal-a. To završavaš u platformi, a Claude ti kaže šta tačno fali i gde.
- **Stanje porudžbina:** porudžbine se ne prihvataju i ne otkazuju kroz Claude.
- **Funkcije koje platforma nema:** na primer raspored na strani proizvoda ili kod koji važi
  jednom po kupcu. Claude ti to kaže otvoreno i predloži najbliže što postoji.
- **Zakazani poslovi:** Claude ne radi sam od sebe u pozadini, na primer „šalji mi izveštaj
  svakog ponedeljka". Radi kad ga pitaš.

## Povratna informacija

Tokom bete nam je svaka primedba dragocena. Najlakše je reći je Claude-u, u razgovoru:

```text
„Javi Brendly timu da ovo nije radilo kako sam očekivao."
„Pošalji predlog: voleo bih da mogu da ..."
```

Claude poruku šalje tvojim rečima, zajedno sa onim što je upravo radio, pa ne moraš ništa da
objašnjavaš dva puta.

## Ažuriranje i uklanjanje

Nove verzije:

```text
/plugin marketplace update brendly
```

Uklanjanje:

```text
/plugin uninstall brendly@brendly
```

## Česta pitanja

**Da li Claude vidi moju lozinku?**
Ne. Prijava se radi na Brendly stranici, a Claude dobija samo dozvolu za rad u tvom nalogu.

**Mogu li da radim sa više prodavnica i tržišta?**
Da. Sve prodavnice iz tvog naloga su dostupne kroz istu prijavu. Reci Claude-u u kojoj radiš,
ili mu reci da prebaci na drugu.

**Šta ako Claude nešto pogrešno izmeni?**
Veće izmene ne prolaze bez tvoje potvrde, a za svaku izmenu dobijaš staro i novo stanje. Tema
se vraća iz snimka, a proizvodi i podešavanja se ispravljaju istim putem kojim su izmenjeni.
