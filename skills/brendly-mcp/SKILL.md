---
name: brendly-mcp
description: Jezgro rada sa Brendly platformom preko MCP-a - prijava i sesija, tržišta RS/BA/HR/EU, izbor prodavnice, redosled poziva, destruktivne akcije, slike i galerija, semantika zaliha. Koristi uvek kad se radi bilo šta preko `brendly_*` alatki, a posebno na početku rada, kod "poveži me", "koje prodavnice imam", "prebaci se na HR", "zašto alatka traži confirm", "kako da pošaljem sliku".
---

# Brendly MCP: kako se radi

MCP je tanak sloj iznad platformskog API-ja. **Sve što uradim vidi se odmah na produkciji** -
nema test okruženja iza ovog servera. Nema ni „poništi".

**Opseg: MCP radi tačno ono što prodavac može i u svojoj Brendly platformi, ništa više.** Kad
korisnik traži nešto što ni platforma ne ume (tekst reklamacione politike, filter po boji u
izlogu, raspored na strani proizvoda, kod koji važi jednom po kupcu), to nije rupa u MCP-u nego
stvar koju proizvod nema. Kažem to otvoreno i ponudim najbliže što postoji, ne izmišljam
zaobilazak i ne obećavam da ću „probati drugačije".

## Prvo tri poziva, uvek tim redom

```
brendly_context whoami       -> ko je prijavljen, koja je prodavnica izabrana
brendly_context list_shops   -> sve prodavnice, sa svih tržišta odjednom
brendly_context select_shop  -> postavlja aktivnu prodavnicu za ostatak sesije
```

Većina alatki radi nad **izabranom prodavnicom**. Ako `whoami` vrati `selectedShopId: null`,
ne pogađam koja je prodavnica u pitanju nego pitam korisnika, osim ako ima tačno jednu.

## Jedna prijava, četiri tržišta

Dizajner se prijavljuje jednom i radi svuda. Nema izbora tržišta pri prijavi i **ne treba nova
prijava za drugo tržište**.

- `list_shops` pita sva tržišta uporedo i vraća jednu listu; svaka prodavnica nosi `market`.
  Tržište koje ne odgovori ne obara poziv nego se javi u `nedostupnaTrzista`.
- `select_shop` uz prodavnicu pamti i njeno tržište, pa se svi sledeći pozivi sami rutiraju.
- EU je submarket HR instance. U listi se vidi kao svoje tržište.
- `brendly_catalog` i `brendly_request` primaju `market` izričito, kad treba pročitati drugo
  tržište od izabranog.

Valuta, PDV i URL-ovi po tržištu: `brendly_markets`.

## Čitanje, izmena i potvrda

- Oblast koja i čita i menja ima dve alatke: `<ime>_read` samo čita, `<ime>_write` menja
  (npr. `brendly_products_read` i `brendly_products_write`). Čitanje nikad ništa ne menja.
- Izmena uvek ide kroz čitanje: šalje se samo ono što se menja, alatka pročita zapis, upiše
  razliku preko njega i pročita ponovo. Ako odgovor kaže da nešto nije potvrđeno, proveri pre
  nego što javiš korisniku da je gotovo.
- Destruktivna izmena traži potvrdu. Klijent koji to ume sam pita korisnika; inače poziv bez
  `confirm:true` vraća opis posledice i ništa ne menja. `confirm:true` ide tek kad je korisnik
  izmenu izričito tražio.
- Tema pravi snimak pre svake izmene i može se vratiti. Gotovi tokovi (kloniranje prodavnice,
  kampanja popusta, SEO za katalog, vraćanje teme) postoje kao prompts, a pravila platforme kao
  resurs `brendly://pravila`.

## Destruktivne akcije su gejtovane

Brisanje, masovne izmene, objava teme i raskidanje platnih integracija bez `confirm: true`
**ništa ne menjaju** - vrate opis posledice. To nije prepreka nego provera.

Pravilo: `confirm: true` šaljem tek kad je korisnik to izričito tražio, i tek pošto sam mu
pokazao šta bi se desilo. Nikad ne šaljem oba poziva zaredom „da uštedim korak".

## Slike: nikad ne teraj čoveka da radi posao koji možeš sam

Pre nego što bilo koji dizajn ubaciš, mora biti spreman za štampu: bez pozadine i najmanje 150 DPI
na stvarnoj veličini otiska. Ko pravi dizajn, kako se traži od modela i kako se priprema piše u
skillu `brendly-katalog`, odeljak „Dizajni".


Redosled biranja, od najboljeg:

| Situacija | Akcija |
|---|---|
| slika već stoji na javnoj https adresi | `brendly_files_write upload_from_url` |
| **imam shell ili mogu HTTP poziv** | `upload_url` → PUT bajtova na dobijenu adresu → `from_upload` |
| ne mogu nijedno od toga | `upload_link` pa `upload_status` |

`upload_link` je poslednja opcija jer njime posao prebacujem na korisnika: dobije link ka
`app.brendly.*/mcp/upload`, otvori ga **prijavljen**, prevuče slike, pa se vrati. Tek tada
zovem `upload_status` sa `sinceFileId` koji mi je `upload_link` vratio.

Sva tri puta se završavaju **istim `fileAssetId`**, koji ide u `designs` pri kreiranju
proizvoda. Bajtove ne provlačim kroz razgovor: 3600×4800 PNG kao base64 je oko 70.000 tokena.

## Semantika koju model ne može da pogodi

- **Zalihe:** stvarna raspoloživost je `inventory.amount - 100`. Vrednost 100 ili manje znači
  nula komada. `brendly_catalog` to već računa u polju `raspolozivo`.
- **Višejezična polja** (naziv, opis, meta): pre izmene pročitaj zapis sa
  `includeTranslations: true`, inače upis pregazi prevode na drugom jeziku.
- **Liste** koriste `page`/`size`/`sortBy`/`sortOrder`, a filteri su tipizovani po alatki
  (`like:`, `eq:`). Galerija je izuzetak: ide bez DTO transformacije, pa su joj parametri
  camelCase.
- **Podrazumevani odgovor je skraćen.** Liste vraćaju samo skalarna polja jer pun zapis za
  stranicu od 25 probija limit. Kad trebaju detalji: `full: true` uz manji `size`.

## Prenos dizajna na drugi tip proizvoda

Koordinate dizajna važe samo za okvir za štampu tog dela, pa se ne prepisuju sa majice na duks
ili šolju. `products_read get` uz svaki dizajn vraća `raspored` izražen kao udeo okvira, koji se
prenosi umesto koordinata. Ceo postupak, sa pragovima i zamkama, je u
`skills/brendly-katalog/reference/prenos-dizajna.md`.

## Popusti, kodovi i obaveštenja

- Datumi popusta i kodova su **UTC** u obliku `yyyy-MM-dd-HH-mm-ss`. Ponoć po Beogradu leti je
  `...-22-00-00` prethodnog dana.
- `active` znači da je popust uključen, ne da važi danas; to kaže `vaziSada` u listi.
- „Ugasi odmah" je `end_now`: kraj za minut, zapis ostaje. `delete` briše i istoriju.
- Obaveštenje u prodavnici i marža na custom proizvodima: šalji samo polje koje menjaš
  (npr. `{active:false}`), ostalo alatka zadrži.

## Izveštaj na kraju posla

Kad završim, korisnik dobija kratak strukturisan pregled, uvek istim redom:

1. **Urađeno** - stavku po stavku, sa imenima i brojevima, ne „ažurirano je nekoliko proizvoda".
2. **Šta je bilo pre, šta je sada** - odgovor izmene nosi `izmene: [{polje, pre, posle}]`.
   Koristim to doslovno; ne prepričavam samo novo stanje, jer korisnik ne može da proveri
   izmenu koju ne vidi u odnosu na staro.
3. **Gde se vidi** - adresa izloga, ekran u aplikaciji, id zapisa.
4. **Nije potvrđeno ili nije prošlo** - sve što je alatka označila kao `nepotvrdjeno`, palo ili
   preskočeno, sa razlogom.
5. **Ostaje** - ako je posao delimičan, šta je sledeći korak.

Ako odgovor ima **`usputnePromene`**, to su polja koja su se promenila a niko ih nije tražio.
To se nikad ne prećutkuje: kažem korisniku šta se još promenilo i predložim proveru.

Za grupne poslove navodim šta se stvarno promenilo (`changes` po stavci), a `unchanged` znači da je
proizvod već bio u traženom stanju. Nikad ne javljam "gotovo" na osnovu odgovora poziva.

## Da li je prodavnica spremna da prima porudžbine

`brendly_shop_settings_read action:"verification_data"` vraća istu listu koju korisnik vidi u
odeljku „Moj Brendly": `hasProducts`, `shopPublished`, `emailVerified`, `areAddressDataFulfilled`,
`hasBillingOptions`, `hasPaymentOptions`, `platformUsageModeSet`, `posVerifiedIfLegalEntity`.

Kad nešto od toga nedostaje, kažem šta tačno fali i gde se to završava. Unos kartice i podataka
za naplatu ne ide kroz MCP: to korisnik radi sam u platformi, i to mu kažem umesto da ćutim ili
pokušavam.

## Granice

MCP ne radi i neće raditi: unos platnih podataka i kartice, uključivanje i isključivanje načina
plaćanja, povezivanje Stripe-a i PayPal-a, prihvatanje uslova korišćenja, menjanje stanja
porudžbine (prihvatanje i otkazivanje, jer to ni prodavac ne radi u platformi), i sve što traži
da se neko potpiše u tuđe ime. Nema ni zakazanih poslova, pragova ni slanja pošte: MCP odgovara
na poziv i tu mu je kraj. Ako korisnik traži ponavljajući zadatak, kažem da to radi zakazani
zadatak kod njega, a MCP je alat koji taj zadatak poziva.

## Kad podataka nema

Prodavnica bez ijedne porudžbine nije kvar. Nula je tačan odgovor i tako ga i saopštavam:
„u tom periodu nema nijedne porudžbine", a ne „ne mogu da pročitam". Isto važi za prazan
katalog, praznu galeriju i praznu istoriju isplata.

## Povratna informacija

`brendly_feedback` šalje Brendly timu ono što korisnik kaže o radu: šta ne radi, šta mu fali,
šta mu se sviđa. Uz poruku sama ide lista poslednjih poziva alatki iz sesije, pa ne prepisujem
šta sam radio.

Ponudim je kad nešto ne uspe, kad korisnik kaže „bilo bi dobro da može..." ili kad je
nezadovoljan. Pošaljem tek kad on to želi, i to njegovim rečima u `message`; svoje objašnjenje
stavim u `context`, a `kind` je `greska`, `predlog`, `pohvala`, `pitanje` ili `drugo`. Posle
kratko zahvalim i nastavim posao, bez dugog izvinjavanja.

## Gde dalje

| Tema | Skill |
|---|---|
| proizvodi, dizajni, cene, kategorije, zalihe | `brendly-katalog` |
| izgled izloga, sekcije, boje, fontovi | `brendly-teme` |
| analitika, porudžbine, povrati, popusti, isplate | `brendly-novac` |
