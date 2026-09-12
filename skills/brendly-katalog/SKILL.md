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

Posle posla proverim rezultat: `action:"preview"` vrati mockup **kao sliku** u odgovoru
(oko 1.500 tokena), pa vidim da li dizajn stoji kako treba.

## Dizajni: odakle dolaze i kako se pripremaju za štampu

Proizvod je dobar koliko i fajl koji ode u štampu. Zato dizajn pre ubacivanja u galeriju mora da
bude **spreman za štampu**, a to se radi bez smaranja korisnika.

### Ko pravi dizajn

Claude ne ume sam da nacrta rastersku sliku. Kad korisnik traži da se dizajn napravi:

| Vrsta dizajna | Put |
|---|---|
| tipografija, natpisi, simboli, linijski crteži, geometrija | nacrtaj **vektorski** (SVG), pa ga pretvori u PNG u punoj rezoluciji zone štampe, sa providnom pozadinom. Ne traži nikakav dodatni nalog i uvek je oštro |
| ilustracija, slikani ili fotorealistični motiv, složen crtež | **ponudi korisniku da poveže alat za slike po svom izboru** (Higgsfield, ChatGPT, Gemini ili drugi). Generisanje je o njegovom trošku, pa alat bira on. Ako je već povezan, koristi ga |

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
5. **Proveri ponovo** veličinu i providnost, pa tek onda ubaci u galeriju.

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

## Isti dizajn na drugom tipu proizvoda

Kad korisnik kaže „napravi isto kao na proizvodu X" ili traži prenos otiska sa majice na duks,
šolju ili ceger: koordinate se ne prepisuju, jer okvir za štampu nije isti. `products_read get`
uz svaki dizajn vraća `raspored` izražen kao udeo okvira i on se prenosi. Pun postupak, sa
pragovima i zamkama, je u `reference/prenos-dizajna.md`; pročitaj ga pre nego što upišeš.

## Zalihe

`brendly_catalog` vraća polje `raspolozivo` = `inventory.amount - 100`. Vrednost `amount`
manja ili jednaka 100 znači **nula komada**, ne malo komada. Nikad ne izveštavam sirov
`amount` korisniku.
