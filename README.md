# Brendly za Claude

**Od ideje do prodavnice, u jednom razgovoru.** Kažeš Claude-u šta želiš, a on to uradi u tvom
Brendly nalogu: osmisli kolekciju, napravi proizvode, otvori prodavnice, uredi izlog i sve pripremi
za prodaju. Svaki korak ti potvrdi, a sve veće izmene radi tek uz tvoje odobrenje.

```text
„Napravi mi brend na temu horoskopa: po jedan dizajn za svaki od 12 znakova, na majicama i
duksevima u bojama koje pristaju svakom znaku. Otvori prodavnice za Srbiju, Hrvatsku, Bosnu i
Hercegovinu i EU, sa opisima na jeziku tržišta, kategorijama po elementima i sređenim izlogom,
i objavi sve tako da mogu da primam porudžbine."
```

A svakodnevno, nad postojećim katalogom:

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
- [Od ideje do prodavnice](#od-ideje-do-prodavnice)
- [Mogućnosti](#mogućnosti)
- [Kako Claude radi sa tvojom prodavnicom](#kako-claude-radi-sa-tvojom-prodavnicom)
- [Povratna informacija](#povratna-informacija)
- [Ažuriranje i uklanjanje](#ažuriranje-i-uklanjanje)
- [Česta pitanja](#česta-pitanja)

---

## Šta dobijaš

| Deo | Šta radi |
|---|---|
| **Brendly server** | Veza sa tvojim Brendly nalogom preko `https://mcp.brendly.rs/mcp`. Daje Claude-u iste mogućnosti koje imaš u Brendly platformi. |
| **Uputstva** | Pet skupova znanja koje Claude sam koristi kad zatreba: povezivanje naloga, rad sa platformom, katalog i proizvodi, izgled izloga, brojke i novac. U Claude Code-u dolaze kao skillovi uz plugin, a u Claude aplikaciji ih server daje sam. Zahvaljujući njima Claude zna kako se šta radi na Brendly-ju, bez objašnjavanja sa tvoje strane. |

## Instalacija

**Potrebno:** Claude nalog i Brendly nalog. Lozinka ostaje na Brendly platformi i nikad ne prolazi
kroz Claude: pri prvom korišćenju otvara se Brendly stranica za odobravanje pristupa, prijaviš se
kao i inače i klikneš **Dozvoli**.

Jedna prijava pokriva ceo nalog: sve tvoje prodavnice, na svim tržištima (Srbija, Hrvatska,
Bosna i Hercegovina i EU), dostupne su odmah. Claude ih izlista i radi u onoj koju izabereš,
isto kao što tržište menjaš u samoj platformi.

### Claude aplikacija (Desktop ili claude.ai)

Radi na svim nalozima, i na besplatnom.

1. Otvori **Settings**, pa **Connectors**.
2. Klikni **Add custom connector**.
3. Ime: `Brendly`, adresa servera: `https://mcp.brendly.rs/mcp`.
4. Klikni **Add**, pa **Connect** i odobri pristup na Brendly stranici.
5. U novom razgovoru, preko dugmeta **+** pa **Connectors**, proveri da je Brendly uključen.

Claude tada uputstva o tome kako Brendly radi učitava sa servera kad mu zatrebaju (alatka
`brendly_uputstva`), pa ti ne treba ništa više. Na Team i Enterprise nalozima konektor dodaje
administrator organizacije.

### Claude Code (plugin)

Claude Code je Claude koji radi na tvom računaru: kartica **Code** u Claude Desktop aplikaciji ili
program za terminal ([claude.com/claude-code](https://claude.com/claude-code)). Deo je plaćenih
naloga (Pro i Max). Plugin pored veze donosi i ista uputstva kao skillove, koje Claude učitava
sam kad zatrebaju.

U polje za poruku unesi, jednu po jednu:

```text
/plugin marketplace add brendly-hub/brendly-plugin
/plugin install brendly@brendly
```

<details>
<summary>Samo server, bez plugina</summary>

```text
claude mcp add --transport http brendly https://mcp.brendly.rs/mcp
```

Radi isto kao i konektor u aplikaciji. Pri prvom razgovoru Claude sam ponudi da uputstva
instalira kao skillove na tvoj računar (jednom, uz tvoju potvrdu), pa dalje radi isto kao sa
pluginom.

</details>

## Prvi razgovor

Dobar početak je da Claude upozna tvoj nalog:

```text
„Koje prodavnice imam i šta je u njima?"
„Da li mi je prodavnica spremna da prima porudžbine? Šta fali?"
„Pokaži mi šta imam u katalogu i koji proizvodi nisu objavljeni."
```

> **Pro savet:** kad god zapneš i nije ti jasno gde si i šta dalje, reci Claude-u baš tako:
> „Zapeo sam, ne znam gde sam ni šta sledeće." On pogleda stanje i vodi te korak po korak.

Posle svakog posla Claude ti kaže šta je uradio, šta je bilo pre a šta je sada, gde to možeš da
vidiš i šta, ako išta, nije uspelo.

---

## Od ideje do prodavnice

Najveća vrednost plugina je u tome što Claude ceo put od teme do prodavnice spremne za
porudžbine prođe sam, a ti odobravaš ključne korake.

```text
„Napravi mi brend na temu horoskopa za sva četiri tržišta i pripremi ga za prodaju."
```

Šta Claude tada radi, redom:

1. **Osmisli kolekciju.** Predloži ime brenda, stil, dvanaest motiva i koje tipove proizvoda i
   boje ima smisla koristiti, i pita te pre nego što išta napravi.
2. **Otvori prodavnice.** Po jednu na svakom tržištu koje izabereš, sa imenom, opisom, SEO poljima
   i jezikom tržišta.
3. **Napravi proizvode.** Dizajne ubaci u galeriju i od njih napravi proizvode u serijama do 200
   odjednom, sa nazivima, opisima i maržama za svaku prodavnicu.
4. **Složi katalog.** Kategorije, glavne boje i redosled, isto na svim tržištima.
5. **Uredi izlog.** Boje i pismo u duhu brenda, logo, naslovna sekcija i istaknuti proizvodi.
6. **Objavi i proveri.** Objavi proizvode i prodavnice, pa za svaku proveri da li je spremna da
   prima porudžbine. Ako nešto mora da završiš sam u platformi, kaže ti tačno šta i gde.

Na kraju dobijaš pregled: koje prodavnice postoje, koliko proizvoda je u svakoj, adrese izloga i
šta je, ako išta, ostalo da se uradi.

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

## Povratna informacija

Tokom bete nam je svaka primedba dragocena. Najlakše je reći je Claude-u, u razgovoru:

```text
„Javi Brendly timu da ovo nije radilo kako sam očekivao."
„Pošalji predlog: voleo bih da mogu da ..."
```

Claude poruku šalje tvojim rečima, zajedno sa onim što je upravo radio, pa ne moraš ništa da
objašnjavaš dva puta.

## Ažuriranje i uklanjanje

U Claude aplikaciji nema šta da se ažurira: server je uvek najnoviji. Konektor uklanjaš u
**Settings**, **Connectors**.

U Claude Code-u nove verzije plugina:

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

**Koje podatke plugin čuva?**
Razgovor sa Claude-om ne dolazi do nas i ne čuvamo ga. Čuvamo tvoju sesiju (šifrovane tokene),
trag o tome koja je alatka pozvana i kako je prošla, i povratnu informaciju kad je sam pošalješ.
Sve je opisano u [Politici privatnosti](PRIVACY.md).

## Privatnost

Šta plugin i server prikupljaju, zašto, koliko dugo to čuvaju i koja su tvoja prava piše u
[Politici privatnosti](PRIVACY.md). Korišćenje same Brendly platforme pokriva
[Politika privatnosti Brendly-ja](https://brendly.rs/pomoc/politika-privatnosti/).
