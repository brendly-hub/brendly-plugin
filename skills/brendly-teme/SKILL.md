---
name: brendly-teme
description: Rad sa temama Brendly izloga preko MCP-a - izgled početne strane, boje, fontovi, logo, baner, kategorije i izabrani proizvodi. Koristi za "promeni izgled prodavnice", "sredi početnu stranu", "promeni boje/font izloga", "postavi baner", "prebaci temu na drugi brend", "dodaj sekciju kategorija", "objavi temu". Tržišta RS/BA/HR/EU.
---

# Brendly: teme izloga

Citam kroz `brendly_themes_read`, a menjam kroz `brendly_themes_write`. Pre svake izmene alatka
sama pravi snimak teme; ako izmena napravi problem, `brendly_themes_read action:"versions"` pa
`brendly_themes_write action:"restore_version"`, pa objava. Menjam izgled izloga: globalne boje i pismo, zaglavlje, podnožje i
sekcije početne strane.

**Objava je odmah vidljiva kupcima.** Rad ide u draft, pa se objavljuje zasebno. Dok ne
pozovem `publish`, živi izlog se ne menja - to je moja mreža i koristim je.

## Šta tema stvarno pokriva

Ovo je prvo što treba reći korisniku ako traži više:

| Pokriveno temom | Nije tema |
|---|---|
| početna strana (6 sekcija) | katalog `/shop` |
| zaglavlje i podnožje | strana proizvoda |
| `primary-color`, `secondary-color` | korpa i kasa |
| boja podnožja i dugmadi | praćenje porudžbine, reklamacija |
| `font-family` (bilo koji Google font) | |

Boje i pismo važe na celom izlogu. Raspored - samo na početnoj. Ako korisnik traži izmenu
kataloga ili strane proizvoda, kažem da to tema ne može, ne pokušavam da zaobiđem.

Tačnije, jer se lako pogreši: tema ima dve stranice, Početnu i Prodavnicu, ali se **sekcije
renderuju samo na Početnoj**. Stranica Prodavnica služi kao stavka menija i prekidač; sam katalog
je zaseban ekran izloga. Tema ima **jedno pismo i pet boja** (`primary-color`, `secondary-color`,
boja podnožja i teksta podnožja, boja dugmeta, tekst dugmeta, boja dugmeta na prelazu). Font
naslova, veličina slova i boja teksta ne postoje ni u temi ni u editoru platforme; ako ih neko
upiše, izlog ih neće koristiti. Biblioteke sekcija nema: sekcije dolaze sa temom, mogu da se
pale, gase, premeštaju i kopiraju, ali se nove ne prave.

## Tok rada

```
themes_read list               -> id teme (obično jedna, aktivna)
themes_read summary            -> stranice, sekcije i elementi nacrta, bez 285 KB definicije
themes_write patch_draft       -> izmene po oznaci elementa + globalni stil
   provera izgleda             -> vidi "Provera" niže
themes_write publish           -> uživo
```

Za drugu prodavnicu ili drugu temu: `themes_read compare` (compareThemeId, sourceShopId) kaže
po čemu se razlikuju, a `themes_write copy_from` (sourceThemeId, sourceShopId) preslika živu temu
te prodavnice u nacrt. Posle preslikavanja pregledaj tekstove na jeziku tržišta i linkove.

`patch_draft` je jedini razuman put. **Ne koristi `update_draft`**: definicija je oko 285 KB
kroz pet preloma i ne može se smisleno provući kroz poziv.

Jedna izmena po oznaci pogađa **sve prelome odjednom** - odgovor vraća `pogodaka` po oznaci
(očekuj 5). Promašena oznaka odbija ceo poziv pre upisa, pa je oznaka pogrešna.

Samo telefon, tablet ili računar: `breakpoints: ["mobile"]` (ili `tablet`, `desktop`, ili tačan
media upit). Tada `pogodaka` broji samo te prelome.

## Stranice u meniju i sekcije

| Traži se | Akcija |
|---|---|
| nova stavka menija | `add_page` name, href (`/shop`, `/shop/category/<slug>`, `/help`, `/reclamation`, `/order-shipping-status`), opciono pageId posle koje ide |
| drugi redosled menija | `reorder_pages` order: id-jevi **svih** stranica novim redom |
| skloni stavku | `remove_page` (samo dodate stranice; početna i sistemske se isključuju sa `active:false`) |
| još jedna sekcija istog oblika | `copy_section` sectionId izvora, afterSectionId; nova dobija nove oznake (npr. `1-7`, `1-7-1`) |
| sekcija sa druge teme ili prodavnice | `copy_section` uz sourceThemeId i sourceShopId |
| pomeri sekciju | `move_section` sectionId, afterSectionId ili toTop |
| ukloni sekciju zauvek | `remove_section` (za privremeno: `patch_draft {id, active:false}`) |

Href koji izlog nema se odbija, jer bi stavka vodila na 404. Kopiju sekcije posle popunjavam
kroz `patch_draft` po novim oznakama, koje vraća `summary`.

Ako je draft u lošem stanju: `discard_draft` ga vrati na živo stanje. Bezbedno.

## Oznake elemenata

Iste su u svakoj Brendly temi jer sve prodavnice nose kopiju istog startera. Puna mapa je u
`reference/elementi.md`. Najčešće:

| Oznaka | Šta je |
|---|---|
| `e-1` / `e-1-1-1` | zaglavlje / logo |
| `e-2` | podnožje |
| `1-1`, `1-1-1`, `1-1-2` | baner: pozadina, naslov, dugme |
| `1-2-1-1` | naslov sekcije kategorija |
| `1-2-2-1..3` | tri pločice kategorija (slika `-1`, naziv `-2`) |
| `1-3-1-1`, `1-3-1-2`, `1-3-2-1` | O nama: naslov, tekst, slika |
| `1-4-1-1`, `1-4-2-1..5`, `1-4-3-1` | proizvodi: naslov, pet pločica, dugme |
| `1-5`, `1-6` | „Naši kupci" i Instagram |

Putanje u `set` su tačkaste: `data.text`, `data.src`, `style.background-color`,
`navigation.href`, `background.image`.

## Broj stavki u sekciji

Sekcija ne raste dodavanjem elemenata. Postoje **gotove varijante** i uključuje se jedna:

- kategorije: `1-2-3` (2), `1-2-2` (3, podrazumevano), `1-2-4` (4), `1-2-5` (6)
- proizvodi: `1-4-4` (3), `1-4-5` (4), `1-4-2` (5, podrazumevano), `1-4-6` (6)
- kupci: `1-5-3` (3), `1-5-4` (4), `1-5-5` (5), `1-5-2` (6, podrazumevano)

Za četiri kategorije: `{"id":"1-2-2","active":false}` i `{"id":"1-2-4","active":true}`.
Celu sekciju gasim preko njenog korena (`1-5`, `1-6`).

## Globalni stil

Kroz ulaz `style`, ne kroz `patches`:

```
"font-family"                            bilo koji Google font
"primary-color"                          dugmad i akcenti
"secondary-color"                        tekst na dugmadi i u podnožju
"siteElements.footerColor.value"         pozadina podnožja
"siteElements.buttonHoverColor.value"    dugme pod mišem
```

To je **sve** što je globalno. Sve ostalo se postavlja po elementu kroz `style.*`. Ključ koji
tema nema (npr. `heading-font-family`) alatka odbija i nabroji postojeće: izlog ga ne bi čitao.

## Zamke koje sam platio

- **`data.src` ima prednost nad `srcFn`.** Logo u zaglavlju (`e-1-1-1`) inače dolazi iz
  zapisa prodavnice; postavljanjem `data.src` menjam ga samo za temu, bez otpremanja.
- **Pločice nisu klikabilne dok se ne uključe.** Za kategorije i proizvode treba
  `navigation.enabled: true` i `clickable: true`. Podrazumevana tema ih nema.
- **Veze:** kategorija je slug (`navigation.href: "Praistorija"` → `/shop/category/Praistorija`),
  proizvod je objekat (`navigation.href.value: "Sve/<tip-slug>/<Naziv>"`). Tačne slugove
  uzimam sa `/shop`, ne izmišljam ih.
- **Font sa svim težinama.** Izlog traži `wght@300;400;500;600;700`. Google tiho servira samo
  postojeće, pa PT Serif (400 i 700) radi - ali ne očekuj polu-debelo.
- **Boje zakucane u izlogu.** Neke komponente imaju boju u SCSS-u i tema ih ne dohvata, npr.
  bela pozadina pretrage (`.app-theme-search-parser > div`). Ne trošim poteze na njih;
  prilagodim se ili javim da je ograničenje izloga.
- **Piši za stvarnu veličinu.** Slika koja se prikazuje na 177 px mora imati slova srazmerno
  veća nego što deluje u izvoru. Merim, ne procenjujem.

## Slike

Element ima polje `resolution` sa preporučenom veličinom (npr. `600px x 600px`). Slike
moraju biti na Brendly hostu - `d1xt6hwjrm2xu8.cloudfront.net` je bela lista.

Pozadina banera se postavlja na **dva mesta odjednom**, inače se razilaze:

```
"background.image": "<url>"
"style.background-image": "linear-gradient(...), url('<url>')"
```

Pozadina se seče po `cover`, a odnos stranica nije isti po prelomima. Merim na živom izlogu
gde tema ispisuje naslov i dugme, pa motive držim izvan tog pojasa.

## Provera

Izlog kešira SSR stranicu, pa posle objave dodajem parametar (`/?v=2`) da ne gledam staro.

Ako imam pregledač: snimim početnu na širokom i na 390 px. Tražim preklapanja, odsečen
tekst, belu prazninu i boje koje se ne slažu sa ostatkom izloga.

Ako ga nemam: kažem korisniku šta sam promenio i da to pogleda. **Ne tvrdim da izgleda dobro
ako nisam video.**

## Detalji

`reference/elementi.md` - puna mapa oznaka, tipovi elemenata i njihova polja.
