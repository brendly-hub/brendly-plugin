# Prenos dizajna na drugi tip proizvoda

Ovo čitaj kad korisnik traži „napravi isto kao na proizvodu X" ili prenos otiska sa jednog tipa
proizvoda na drugi. Za obično kreiranje i izmenu nije potrebno.

Koordinate dizajna (`left`, `top`, `scaleX`) važe samo za okvir za štampu tog dela. Majica, duks,
šolja i ceger imaju različite okvire, pa prepisana geometrija promaši. Zato `products_read get` uz
svaki dizajn vraća i `raspored`: `scale` (udeo širine okvira), `offsetX`/`offsetY` (pomeraj od
sredine, kao udeo), `angle`, `vrstaOtiska` (znak do 0.35, srednji, preko cele zone od 0.85),
`okvirOdnosStranica` i `otisakMm`. Ciljni okvir pročitaj iz `brendly_catalog product_type`: svaki
deo ima `zonaStampe`, `referentnaZonaMm`, `okvirOdnosStranica` i `dozvoljavaBojuPozadine`.

### Postupak

1. **Spoji delove po ulozi, ne po id-u.** Id dela se razlikuje po tipu proizvoda. Prednji deo
   prepoznaj po `orderNumber: 1`, zadnji po 2. Deo koji cilj nema (duks bez leđa, šolja sa jednim
   delom) preskoči i to reci. Delove koje izvor nema ostavi prazne.
2. **Uporedi oblik okvira.** `r = okvirOdnosStranica(cilj) / okvirOdnosStranica(izvor)`.
   - `0.85 ≤ r ≤ 1.18` (majica na majicu, majica na duks): **preslikaj** raspored.
   - izvan toga (šolja, ceger, kapa): **ne preslikavaj**. Pošalji `preset: "fit"`, ili `"fit-top"`
     ako je izvor bio pri vrhu (`offsetY < -0.15`), i reci korisniku da je dizajn prilagođen
     drugom okviru.
3. **Veličina pri preslikavanju**, po `vrstaOtiska`:
   - `znak` (mali logo): čuvaj **stvarnu veličinu**, ne udeo.
     `scale = otisakMm.sirina / referentSizePrintAreaWidth(cilj)`. Inače logo sa majice na duksu
     ispadne veći nego što je zamišljen.
   - `preko cele zone`: čuvaj udeo, pošalji `scale` kakav jeste.
   - `srednji`: podrazumevano čuvaj udeo, ali ako bi se stvarna veličina promenila za više od
     četvrtine, reci korisniku obe mogućnosti pre nego što upišeš.
4. **Pomeraje** (`offsetX`, `offsetY`) prenosi uvek kao udeo, i kod preslikavanja i kad menjaš
   veličinu. Logo na strani srca tako ostaje na strani srca.
5. **Nagib** se prenosi samo bez preseta. Gotov raspored (`fit`, `fit-top`) uspravlja dizajn, pa
   `angle` iz izvora izgubiš ako uz njega pošalješ preset.
6. **Proveri posle posla.** Pročitaj cilj i uporedi dobijeni `raspored` sa traženim. Java smanjuje
   otisak da stane u okvir i pomera ga unutar sigurne ivice, pa ako je rezultat manji ili pomeren,
   reci koliko.

### Zamke

- **Slika dizajna.** Prvo `fileAssetId`. Ako je prazan (dizajn nije iz galerije), uzmi
  `productObjectImageUrl` kao `imageUrl`: to je javna adresa i radi kao izvor.
- **Više dizajna na jednom delu.** Svaki ima svoj `index` i svoj `raspored`; prenesi svaki
  posebno i zadrži redosled.
- **Kreiranje ne prima pun raspored**, samo `fit` i `fit-top`. Zato je „napravi isto kao X" dva
  koraka: `create` pa `products_bulk update` sa `designPlacement`. To su dva renderovanja, pa
  posao traje duže; reci to korisniku unapred.
- **Boja pozadine otiska** se prenosi samo na deo koji je dozvoljava
  (`dozvoljavaBojuPozadine: true`), inače je izostavi i reci zašto.
- **Tip proizvoda mora da postoji na tržištu prodavnice.** Ceger i šolja ne postoje na svim
  tržištima; proveri kroz `catalog product_types` pre nego što obećaš.
- **Tamna i svetla osnova.** Ako izvor ima svetlu glavnu boju a cilj tamnu (ili obrnuto), dizajn
  sa belim elementima može da nestane. Predloži pregled mockupa ili drugu glavnu boju.
- **Korisnikova reč je jača od izvora.** „Oversize duks" znači veliki otisak pri vrhu
  (`preset: "fit-top"`), čak i ako je na izvoru bio mali logo. Preslikavaj samo ono što korisnik
  nije izričito zadao.
- **Dizajn uz ivicu.** Kad je `|offset| > 0.35`, na manjem okviru ga sigurna ivica gurne ka
  sredini. Posle posla proveri koliko je pomeren.

