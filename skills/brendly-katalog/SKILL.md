---
name: brendly-katalog
description: Rad sa Brendly katalogom preko MCP-a - pravljenje i priprema dizajna za štampu, kreiranje proizvoda od dizajna, tipovi proizvoda, boje, veličine, cene i cenovne grupe, kategorije, objava, mockup slike i galerija dizajna. Koristi za "napravi dizajn", "napravi kolekciju", "napravi proizvode", "dodaj dizajn na majicu", "ovaj dizajn je mutan", "podigni cene", "objavi proizvode", "dodaj kategoriju", "koje boje ima ovaj tip", "koliko ima zaliha", "obriši proizvode".
---

# Brendly: katalog

Radim nad **izabranom prodavnicom**. Ako nije izabrana, prvo `brendly_context select_shop` -
vidi skill `brendly-mcp`.

## Šifarnici pre svega

Ništa se ne pogađa napamet. Tipovi proizvoda, boje i veličine se čitaju:

```
brendly_catalog product_types        -> svi tipovi na tržištu (id, naziv, cene)
brendly_catalog shop_product_types   -> tipovi dostupni ovoj prodavnici
brendly_catalog product_type         -> jedan tip: delovi, zone štampe, tabela veličina
brendly_catalog colors / sizes       -> šifarnik boja i veličina
```

Zamka: „sve majice" nije jedan tip nego desetak (muška, ženska, unisex, organic, oversize,
dugi rukav, dečja). Kad korisnik kaže „sve majice", nabrojim tipove iz šifarnika i pokažem
mu listu pre nego što bilo šta menjam.

## Kreiranje proizvoda

```
brendly_products_write action:"create"  { productTypeId, name, designs, ... }
brendly_products_read action:"job"     { jobId }        -> praćenje
```

Obavezni su samo **`productTypeId`, `name` i `designs`**. Boje, cene, kategorije i tip štampe
backend izvodi sam iz tipa proizvoda - ne šaljem ih osim ako korisnik traži drugačije.

- Do **200 proizvoda po pozivu**, kroz `products` niz.
- Posao je asinhron, oko 8 sekundi po proizvodu. `create` čeka do 180 sekundi pa vrati
  `jobId`; dalje se prati sa `action:"job"`. `waitSeconds: 0` vraća odmah.
- **Ponovljen naziv na istom tipu daje `SKIPPED`, ne duplikat.** Dedup gleda naziv **i** tip,
  pa isti naziv na drugom tipu proizvoda prolazi kao nov proizvod.
- Dizajn ide kao `imageUrl`, `imageBase64` ili `fileAssetId`. Kako sliku dovesti u galeriju:
  vidi odeljak o slikama u skillu `brendly-mcp`. `fileAssetId` je najjeftiniji put.
- `imageBase64` ima smisla samo kad bajtove daje alat (kod pročita fajl ili ga napravi). Base64
  napisan „iz glave" je uvek pokvaren: model ne može verno da prepiše bajtove, pa slika ispadne
  skraćena ili izmišljena. MCP zato proveri da je to cela PNG, JPEG ili WEBP slika i odbije je pre
  potvrde, sa razlogom. Kad stigne takvo odbijanje, ne pokušavam ponovo drugim base64 nizom ni
  „probnom" slikom 1×1 u prodavnici korisnika, nego biram put iz tabele u `brendly-mcp`.

Posle posla proverim rezultat: `action:"preview"` vrati mockup **kao sliku** u odgovoru
(oko 1.500 tokena), pa vidim da li dizajn stoji kako treba.

## Dizajni: odakle dolaze i kako se pripremaju za štampu

Proizvod je dobar koliko i fajl koji ode u štampu. Zato dizajn pre ubacivanja u galeriju mora da
bude **spreman za štampu**, a to se radi bez smaranja korisnika.

### Ko pravi dizajn

Claude ne ume sam da nacrta rastersku sliku, a najbolji dizajni danas nastaju u modelima za slike.
Zato je redosled uvek isti.

**1. Rasterski dizajn dolazi iz korisnikovog alata za slike.** Brendly ne generiše slike:
generisanje ide preko naloga i o trošku korisnika, u alatu koji on sam bira. Ako je takav alat već
povezan sa Claude-om, koristim ga. Ako nije, a korisnik pita odakle slika, odgovor je činjeničan:

> Slike ne pravim sam. Ako već koristiš neki alat za slike, napravi motiv u njemu i pošalji mi ga,
> ili ga poveži sa Claude-om pa radim direktno iz razgovora. Alati koje ljudi obično koriste za to
> su Higgsfield, ChatGPT i Gemini. Sve ostalo preuzimam ja: opise za model, izbor, pripremu fajla
> za štampu i pravljenje proizvoda.

Alat bira korisnik i plaća ga sam. Nijedan ne preporučujem i nijedan nije potreban za rad sa
Brendly-jem; bez njega se radi vektorski dizajn.

**2. Ako korisnik ne želi da povezuje ništa**, reci mu da mogu i vektorski dizajni koje nacrtaš
sam: natpisi, simboli, linijski crteži, geometrija. Budi iskren da je to uži izbor od modela, ali
je besplatan i uvek oštar za štampu. Takav dizajn crtaš kao SVG i pretvaraš u PNG pune
rezolucije zone štampe, sa providnom pozadinom.

### Više dizajna: pokaži pre nego što nastaviš

Kad korisnik traži više dizajna, **ne pravi sve odjednom pa da ih vidi tek na kraju.** Pogrešan
pravac se tako umnoži, a trošak generisanja je njegov.

1. Napravi **prvi** dizajn, pokaži ga i pitaj da li je to pravac: stil, boje, nivo detalja.
2. Posle potvrde pravi **jedan po jedan** i pokaži svaki čim nastane, uz kratku napomenu šta si
   menjao. Korisnik tako može da zaustavi ili ispravi u hodu.
3. Tek kada su dizajni prihvaćeni, pripremi ih za štampu i napravi proizvode u jednom poslu.

Ako korisnik izričito kaže da ne želi da gleda usput („samo napravi svih dvanaest"), poštuj to,
ali i tada prvo pokaži jedan.

### Kako tražiš sliku od modela

- Samo motiv, izolovan. Nikad majica, mokap, lutka ni scena: to na otisku nema šta da traži.
- Bez pozadine, i to ne opisuj u promptu kao „na beloj podlozi".
- Najveća rezolucija koju alat daje, u odnosu stranica zone štampe (za majicu uspravno, 3:4).
- **OpenAI (ChatGPT, gpt-image):** traži providnu pozadinu i PNG. Ako prompt opiše pozadinu ili
  scenu, providnost se gubi. Veličina mora biti deljiva sa 16, a odnos stranica između 1:3 i 3:1.
- **Gemini:** ne ume providnu pozadinu. Napravi isti motiv jednom na čisto beloj, a jednom na
  čisto crnoj podlozi, pa iz razlike izračunaj stvarnu providnost; to daje čiste ivice i kod
  poluprovidnih delova. Druga mogućnost je jednobojna zelena podloga koja se posle ukloni.
- **Higgsfield:** ima i uklanjanje pozadine i uvećanje; koristi ih u pripremi.

### Priprema pre ubacivanja

Pročitaj zonu štampe iz `brendly_catalog product_type` (`referentnaZonaMm` i
`recommendedFileOptions`), pa:

1. **Izmeri.** Idealno je 300 DPI na stvarnoj veličini otiska: širina u pikselima = širina u mm
   / 25,4 × 300 (majica sa zonom od 271 mm: oko 3.200 px). **Ispod 150 DPI dizajn nije za štampu**,
   jer se tada golim okom vide stepenice.
2. **Uvećaj ako treba**, vernim uvećanjem, ne kreativnim: kreativno uvećanje dodaje detalje kojih
   nije bilo i menja dizajn. Uvećanje ide pre uklanjanja pozadine, jer model na većoj slici daje
   čistije ivice.
3. **Ukloni pozadinu** ako slika nema providnost. Puna bela pozadina se na tamnoj majici odštampa
   kao beli pravougaonik.
4. **Očisti ivice**: bela aura oko motiva, jedva vidljivi pikseli, prazne margine.
5. **Ne šalji više piksela nego što štampa koristi.** Na 300 DPI zona majice (282x376 mm) traži
   3.331x4.441 px, a najveća zona na odeći, hoodica (315x420 mm), 3.720x4.961 px. Sve preko toga
   se pri štampi svakako smanji, a poskupljuje svaki render. Takav dizajn je obično oko 1 MB, a
   i sa puno detalja ispod 10 MB; **ako fajl pređe 10 MB, to je znak da je slika iznad 300 DPI ili
   da je fotografija sačuvana kao PNG** (za fotografiju bez providnosti koristi JPG, višestruko je
   manji). Tvrda granica rendera je 50 MB i 200 MP, preko toga posao odmah pada.
6. **Proveri ponovo** veličinu i providnost, pa tek onda ubaci u galeriju.

Korisniku javi samo ishod, jednom rečenicom („dizajn je uvećan i uklonjena mu je pozadina").
Pitaj ga samo kada priprema nije moguća: na primer kada povezani alat nema uvećanje, a slika je
ispod 150 DPI. Tada mu to reci otvoreno i ponudi da poveže alat koji ima uvećanje; ne pravi
proizvod od dizajna koji nije za štampu.

## Cene

Za masovnu izmenu postoji par koji se uklapa jedan u drugi:

```
brendly_products_bulk price_preview   { productIds }  -> {tip: {stavka: {cena...}}}
   izmeni vrednosti
brendly_products_bulk update_prices   { prices, confirm: true }
```

`price_preview` vraća **tačno oblik koji `update_prices` prima**, pa je izmena bezbedna.
Cene idu po **veličinskim grupama**, ne po pojedinačnoj veličini.

## Izmena postojećih proizvoda

```
brendly_products_bulk action:"update" { productIds, addColorIds, removeColorIds,
                                        categoryIds, published, confirm: true }
brendly_products_read action:"job"         { jobId }        -> praćenje
```

Šalje se **razlika**, ne ceo zapis. Ne pokušavam da pročitam proizvod pa da ga vratim
izmenjenog: čitanje ne vraća osam polja koja upis traži, pa to i ne može da uspe. Server
ih ima i sam sklapa ceo zahtev.

Šta koliko traje:

| Izmena | Render | Trajanje |
|---|---|---|
| cena, kategorija, objava, naziv | ne | odmah |
| dodavanje ili uklanjanje boje | da, ceo skup boja | nekoliko sekundi po proizvodu |

Naziv, opis i meta polja idu **samo uz tačno jedan proizvod**: naziv je jedinstven po
prodavnici i tipu, pa bi isti naziv na više proizvoda pao u pozadini.

Cene se ovom izmenom ne diraju. Za njih je `price_preview` pa `update_prices`.

**Nikad ne brišem proizvod da bih ga „izmenio".** Brisanjem se gube id i URL proizvoda, a
sa njima i sve što na njih pokazuje. Ako izmena ne prolazi, kažem korisniku zašto, ne
zaobilazim.

## Objava i brisanje

- `brendly_products_write action:"publish"` ili `"unpublish"` za jedan proizvod,
  `brendly_products_bulk set_published` za više. Obe zadaju stanje: ponovljen poziv nista ne obrne.
- Izmena jednog proizvoda: `brendly_products_write action:"update"` sa samo poljima koja menjas
  (name, description, metaTitle, metaDescription, categoryIds, primaryCategoryId, primaryColorId,
  published). Alatka procita proizvod, posalje samo stvarnu razliku i kaze sta se promenilo.
- Brisanje i masovne izmene traže `confirm: true` i menjaju živi izlog. Prvo pokažem šta bi
  se desilo, pa tražim potvrdu.

## Koje se slike prikazuju u prodavnici

```
brendly_product_variants_read list          -> sve varijante sa stanjem
brendly_product_variants_write set_chosen   -> jednu sliku prikazi ili sakrij
brendly_product_variants_write sort_chosen  -> izbor i redosled odjednom
brendly_product_variants_write add_photo    -> sopstvena fotografija za boju
```

Kad se proizvod napravi, svaka kombinacija dela i boje dobije svoju sliku. Šta se od toga
vidi u prodavnici bira se ovde.

- `chosen` znači „prikazuje se", `serialNumber` je redosled prikazanih **unutar jedne boje**
  (1, 2, 3...), sakrivene nose 0. Redne brojeve ne računam sam, alatka ih sređuje.
- **Bar jedna slika mora ostati prikazana.** Alatka odbija potez koji bi sakrio sve.
- Slika koja iz sakrivene pređe u prikazanu ide **na kraj reda svoje boje**.
- Prodavnica za listing uzima prvu prikazanu sliku primarne boje, s tim da **fotografija
  prodavca ima prednost** nad mockup-om.

## Kategorije

`brendly_categories_read` (listanje, proizvodi u kategoriji) i `brendly_categories_write`
(kreiranje, izmena, brisanje i dodela proizvoda). Kategorija se
u temi vezuje **slugom**, ne id-em - to je važno ako se odmah posle sređuje izlog.

## Gde ide dizajn na proizvodu

Otisak se odlučuje po redu, prvi uslov koji važi pobeđuje:

1. **„napravi isto kao na proizvodu X"** - pročitam X kroz `products_read get` i pošaljem
   `geometry`, ne `placement`. Detalji u `reference/prenos-dizajna.md`.
2. **korisnik je rekao gde** („na srce", „uz dno", „malo niže") - šaljem taj raspored:
   `chest-heart`, `chest-right`, `fit-bottom`, `fit-top`.
3. **korisnik je samo rekao da napravi proizvod sa dizajnom** - ne šaljem ništa.
   Podrazumevano je `fit-top`: najveći otisak koji staje, poravnat uz gornju ivicu zone.
   To je isto što prodavac u editoru dobije sa dva klika, „uklopi u okvir" pa „poravnaj uz
   vrh", i to je ono što većina njih i uradi.
4. **korisnik je zadao tačnu veličinu ili položaj** - poštujem ga doslovno.

Centrirani `fit` šaljem samo kad neko izričito traži dizajn na sredini: otisak tada visi na
sredini grudi i izgleda kao greška.

Posle posla pročitam proizvod i uporedim otisak sa traženim. Ako je Java smanjila otisak da
stane u okvir, kažem koliko.

## Isti dizajn na drugom tipu proizvoda

Kad korisnik kaže „napravi isto kao na proizvodu X" ili traži prenos otiska sa majice na duks,
šolju ili ceger: koordinate se ne prepisuju, jer okvir za štampu nije isti. `products_read get`
uz svaki dizajn vraća `raspored` izražen kao udeo okvira i on se prenosi. Pun postupak, sa
pragovima i zamkama, je u `reference/prenos-dizajna.md`; pročitaj ga pre nego što upišeš.

## Zalihe

`brendly_catalog` vraća **stanje** po artiklu (boja i veličina), ne broj komada: da li ide
odmah, da li se radi po porudžbini i koliko se čeka. Koliko komada držimo nije podatak za
prodavca, pa ga ne tražim drugim putem niti ga procenjujem iz nečeg drugog.
