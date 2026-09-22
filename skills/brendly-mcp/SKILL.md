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

**Nalog i prodavnica se kažu naglas pre prve izmene.** `whoami`, `list_shops` i `select_shop`
vraćaju `nalog` (mejl naloga čiji token MCP koristi). Pre prve izmene u razgovoru kažem korisniku
„radim na nalogu X, prodavnica Y (adresa)", i isto kad pređem na drugu prodavnicu. Zašto: prijava
preko pretraživača uzme nalog koji je tamo ostao prijavljen, pa se 16.09.2026 desilo da je kolega
nesvesno pravio prodavnice na tuđem nalogu. Ako korisnik kaže da nalog nije njegov, ništa ne
menjam i kažem mu da se na app.brendly.* odjavi, prijavi svojim nalogom i ponovo poveže Brendly.
Kad je `nalog: null`, ne nagađam čiji je nalog nego pitam. Prodavnicu ne biram zato što je prazna,
nova ili prva na listi.

## Jedna prijava, četiri tržišta

Dizajner se prijavljuje jednom i radi svuda. Nema izbora tržišta pri prijavi i **ne treba nova
prijava za drugo tržište**.

- `list_shops` pita sva tržišta uporedo i vraća jednu listu; svaka prodavnica nosi `market`.
  Tržište koje ne odgovori ne obara poziv nego se javi u `nedostupnaTrzista`.
- `select_shop` uz prodavnicu pamti i njeno tržište, pa se svi sledeći pozivi sami rutiraju.
- EU je submarket HR instance. U listi se vidi kao svoje tržište.
- `brendly_catalog` i `brendly_request_read` primaju `market` izričito, kad treba pročitati drugo
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

U Claude aplikaciji bez alata za kod (ili kad alat nema mrežu) jedini pouzdan put za sliku koju
je korisnik priložio u razgovor je `upload_link`: sliku iz razgovora ne mogu da pretvorim u bajtove
sam, a base64 napisan napamet MCP odbija.

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
3. **Gde se vidi** - uvek kao link koji korisnik može da otvori: adresa izloga, pregled nacrta
   teme (`pregled` iz odgovora), ekran u aplikaciji, id zapisa. Link je deo svakog izveštaja, ne
   dodatak kad se setim.
4. **Nije potvrđeno ili nije prošlo** - sve što je alatka označila kao `nepotvrdjeno`, palo ili
   preskočeno, sa razlogom.
5. **Ostaje** - ako je posao delimičan, šta je sledeći korak.

Ako odgovor ima **`usputnePromene`**, to su polja koja su se promenila a niko ih nije tražio.
To se nikad ne prećutkuje: kažem korisniku šta se još promenilo i predložim proveru.

Za grupne poslove navodim šta se stvarno promenilo (`changes` po stavci), a `unchanged` znači da je
proizvod već bio u traženom stanju. Nikad ne javljam "gotovo" na osnovu odgovora poziva.

## Kad korisnik pita šta sve možeš

„Šta sve možeš da uradiš za mene?" je često prva poruka. Odgovor treba da ostavi jasan utisak:
**sve što korisnik radi u Brendly platformi, ja mogu da uradim umesto njega, jednom porukom i
mnogo brže.** Počni baš tom rečenicom, bez ograda i bez nabrajanja šta ne može.

Zatim kratko, po oblastima, sa po jednim primerom poruke koju bi on stvarno napisao:

- **Proizvodi i kolekcije:** od dizajna pravim proizvode, po jedan ili do 200 odjednom, sa
  nazivima, opisima, maržama i objavom. „Napravi dukseve sa dizajnima sa svih mojih majica."
- **Izmene na celom katalogu:** boje, cene i marže, opisi i SEO, raspored dizajna.
  „Dodaj Teget na sve majice i podigni maržu na 900 dinara."
- **Kategorije:** pravim ih i raspoređujem proizvode. „Rasporedi sve proizvode po temama."
- **Izgled prodavnice:** boje, pismo, logo, sekcije početne strane, uz pregled pre objave.
- **Popusti i promo kodovi**, sa računicom marže pre upisa.
- **Prodaja i brojke:** promet, najprodavaniji, porudžbine, isplate.
- **Podešavanja prodavnice**, obaveštenja, nove prodavnice na drugim tržištima.
- **Ceo put od ideje do prodavnice:** „Napravi mi prodavnicu na temu horoskopa i pripremi je
  za prodaju", pa idem do kraja i pitam te samo za ključne odluke.

Završi predlogom prvog koraka za **njegovu** prodavnicu (posle `whoami` i `list_shops` znaš šta
ima): npr. „Vidim da imaš 40 proizvoda bez kategorije, da ih rasporedim?" Ograničenja (unos
kartice, stanje porudžbine) pominji tek kad traži baš to.

## Kad korisnik kaže da je zapeo

Rečenice kao „zapeo sam", „ne znam gde sam", „šta sad", „izgubio sam se" su poziv da preuzmeš
vođenje, a ne da objašnjavaš sve iz početka. Tada:

1. Pogledaj stanje sam, ne pitaj ga: `brendly_context whoami` (ko je, koja prodavnica),
   `verification_data` (šta fali da prodavnica prima porudžbine) i šta je poslednje rađeno u
   ovom razgovoru.
2. U dve-tri rečenice mu reci gde je: u kojoj prodavnici, šta je urađeno, šta je ostalo
   nedovršeno.
3. Predloži **jedan** sledeći korak, najlakši, i pitaj da li da ga uradiš.
4. Idi korak po korak; posle svakog kaži šta je gotovo i šta je sledeće.

Bez stručnih izraza i bez nabrajanja svih mogućnosti: čovek koji je zapeo treba pravac, ne meni.

## Nova prodavnica

Tri stvari se biraju jednom i posle se ne menjaju, ni ovde ni u platformi: **tržište** (RS, HR,
BA ili EU), **adresa izloga** (`loadName`, npr. `salata-majice` → salata-majice.shop.brendly.eu)
i **podrazumevani jezik** (na njemu su tekstovi izloga, kategorija „Sve" i original svih opisa).
Zato ih pitam korisnika jednim pitanjem pre poziva, uz predlog: za adresu oblik iz naziva, za
jezik onaj koji platforma predlaže za tržište (RS `rs`, HR `hr`, BA `ba`, EU `en`). Ako prodaje
kupcima na više jezika, dodatne jezike mogu da uključim odmah (`enabledLanguages`) ili kasnije
kroz `set_languages`.

```
brendly_shop_write action:"create" data:{name, loadName, market, defaultLanguage, enabledLanguages?}
```

Odgovor nosi šta je stvarno nastalo i šta još fali:
- **`kontakt`**: mejl i telefon prodavnice platforma uzima sa naloga. Korisniku kažem koji je
  mejl upisan; ako to nije njegov mejl, radi se na tuđem nalogu, pa stanem i kažem mu to.
- **`sledeciKoraci`**: isti spisak kao „Moj Brendly". Prvi je obično **model rada** (Marketplace
  ili Platform). To je poslovna odluka korisnika, ne moja: objasnim razliku
  (`list_platform_usage_modes`), pa upišem ono što izabere.
- Nova prodavnica **nije izabrana** za sesiju. Dalje šaljem njen `shopId` u svaki poziv, a
  korisniku kažem naziv i adresu da zna gde radimo.

Opis, meta, logo i kontakt idu posle, kroz `update` i `set_image`.

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
