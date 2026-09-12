# Elementi teme

Izvuceno masinski iz zive teme (starter "Junior", koji nose sve prodavnice).
Oznake su iste u svakoj temi i u svih pet preloma.

## Tipovi elemenata i njihova polja

| tip | polja |
|---|---|
| `button` | `active`, `class`, `data.text`, `id`, `name`, `navigation.editable`, `navigation.enabled`, `navigation.href`, `navigation.hrefType`, `position`, `style`, `textStyle`, `type` |
| `cart-toggler` | `active`, `id`, `position`, `style`, `type` |
| `connected-shops` | `active`, `id`, `position`, `style`, `type` |
| `footer` | `class`, `id`, `style`, `type` |
| `header` | `id`, `style`, `type` |
| `icon` | `active`, `data.icon`, `id`, `navigation.enabled`, `navigation.hrefFn`, `navigation.hrefTarget`, `navigation.hrefType`, `position`, `style`, `type` |
| `image` | `active`, `data.src`, `data.srcFn`, `id`, `name`, `navigation.editable`, `navigation.enabled`, `navigation.href`, `navigation.hrefType`, `position`, `resolution`, `style`, `type` |
| `input` | `action`, `active`, `class`, `data.ctaLabel`, `data.placeholder`, `data.regexMessage`, `data.regexType`, `id`, `position`, `style`, `type` |
| `language-switcher` | `active`, `id`, `position`, `style`, `type` |
| `nav` | `active`, `id`, `position`, `style`, `type` |
| `search` | `active`, `id`, `position`, `style`, `type` |
| `section` | `active`, `allowElementsSorting`, `background`, `canHaveDifferentSettingsOnMultipleScreens`, `clickable`, `grid`, `homepage`, `id`, `name`, `navigation.editable`, `navigation.href`, `navigation.hrefType`, `nonToggleable`, `position`, `style`, `type` |
| `text` | `active`, `class`, `data.text`, `data.textFn`, `id`, `name`, `navigation.enabled`, `navigation.href`, `navigation.hrefTarget`, `navigation.hrefType`, `position`, `style`, `subtype`, `textStyle`, `type` |
| `textarea` | `active`, `class`, `data.text`, `id`, `name`, `position`, `style`, `subtype`, `textStyle`, `type` |

## Mapa oznaka

### Zaglavlje i podnozje

- `e-1` header
  - `e-1-1` section
    - `e-1-1-1` image - (iz podataka prodavnice)
    - `e-1-1-2` nav
    - `e-1-1-3` connected-shops
    - `e-1-1-5` search
    - `e-1-1-4` language-switcher
    - `e-1-1-6` cart-toggler
- `e-2` footer
  - `e-2-1` text - Saznaj kada stignu novi proizvodi
  - `e-2-2` input
  - `e-2-3` text - (iz podataka prodavnice)
  - `e-2-4` section
    - `e-2-4-1` icon - fab fa-instagram
    - `e-2-4-2` icon - fab fa-facebook
    - `e-2-4-3` icon - fab fa-youtube
    - `e-2-4-4` icon - fab fa-twitter
    - `e-2-4-5` icon - fab fa-tiktok
  - `e-2-5` section
    - `e-2-5-1` text - (iz podataka prodavnice)
    - `e-2-5-2` text - (iz podataka prodavnice)
  - `e-2-6` text - (iz podataka prodavnice)
  - `e-2-7` section
    - `e-2-7-1` text - Prijavi reklamaciju
    - `e-2-7-2` text - Uslovi prodaje
    - `e-2-7-3` text - Povrat i zamena
    - `e-2-7-4` text - Poručivanje i dostava
  - `e-2-8` text - (iz podataka prodavnice)

### Pocetna strana

- `1` section - Početna
  - `1-1` section - Baner
    - `1-1-1` textarea - Glavni tekst - PREDSTAVI SE
    - `1-1-2` button - Dugme - POGLEDAJ PROIZVODE
  - `1-2` section - Kategorije
    - `1-2-1` section - Naslov sekcije
      - `1-2-1-1` text - Tekst - IZABRANE KATEGORIJE
    - `1-2-3` section - 2 kategorije  **iskljuceno**
      - `1-2-3-1` section - Kategorija 1
        - `1-2-3-1-1` image - Fotografija - (slika)
        - `1-2-3-1-2` text - Naziv - Kategorija
      - `1-2-3-2` section - Kategorija 2
        - `1-2-3-2-1` image - Fotografija - (slika)
        - `1-2-3-2-2` text - Naziv - Kategorija
    - `1-2-2` section - 3 kategorije
      - `1-2-2-1` section - Kategorija 1
        - `1-2-2-1-1` image - Fotografija - (slika)
        - `1-2-2-1-2` text - Naziv - Kategorija
      - `1-2-2-2` section - Kategorija 2
        - `1-2-2-2-1` image - Fotografija - (slika)
        - `1-2-2-2-2` text - Naziv - Kategorija
      - `1-2-2-3` section - Kategorija 3
        - `1-2-2-3-1` image - Fotografija - (slika)
        - `1-2-2-3-2` text - Naziv - Kategorija
    - `1-2-4` section - 4 kategorije  **iskljuceno**
      - `1-2-4-1` section - Kategorija 1
        - `1-2-4-1-1` image - Fotografija - (slika)
        - `1-2-4-1-2` text - Naziv - Kategorija
      - `1-2-4-2` section - Kategorija 2
        - `1-2-4-2-1` image - Fotografija - (slika)
        - `1-2-4-2-2` text - Naziv - Kategorija
      - `1-2-4-3` section - Kategorija 3
        - `1-2-4-3-1` image - Fotografija - (slika)
        - `1-2-4-3-2` text - Naziv - Kategorija
      - `1-2-4-4` section - Kategorija 4
        - `1-2-4-4-1` image - Fotografija - (slika)
        - `1-2-4-4-2` text - Naziv - Kategorija
    - `1-2-5` section - 6 kategorija  **iskljuceno**
      - `1-2-5-1` section - Kategorija 1
        - `1-2-5-1-1` image - Fotografija - (slika)
        - `1-2-5-1-2` text - Naziv - Kategorija
      - `1-2-5-2` section - Kategorija 2
        - `1-2-5-2-1` image - Fotografija - (slika)
        - `1-2-5-2-2` text - Naziv - Kategorija
      - `1-2-5-3` section - Kategorija 3
        - `1-2-5-3-1` image - Fotografija - (slika)
        - `1-2-5-3-2` text - Naziv - Kategorija
      - `1-2-5-4` section - Kategorija 4
        - `1-2-5-4-1` image - Fotografija - (slika)
        - `1-2-5-4-2` text - Naziv - Kategorija
      - `1-2-5-5` section - Kategorija 5
        - `1-2-5-5-1` image - Fotografija - (slika)
        - `1-2-5-5-2` text - Naziv - Kategorija
      - `1-2-5-6` section - Kategorija 6
        - `1-2-5-6-1` image - Fotografija - (slika)
        - `1-2-5-6-2` text - Naziv - Kategorija
  - `1-3` section - O nama
    - `1-3-1` section - Tekst
      - `1-3-1-1` text - Naslov - O nama
      - `1-3-1-2` textarea - Opis - Lorem ipsum dolor sit amet, consec...
    - `1-3-2` section - Fotografija
      - `1-3-2-1` image - Fotografija - (slika)
  - `1-4` section - Proizvodi
    - `1-4-1` section - Naslov sekcije
      - `1-4-1-1` text - Tekst - IZABRANI PROIZVODI
    - `1-4-4` section - 3 proizvoda  **iskljuceno**
      - `1-4-4-1` section - Proizvod 1
        - `1-4-4-1-1` image - Fotografija - (slika)
        - `1-4-4-1-2` text - Naziv - Proizvod
        - `1-4-4-1-3` text - Cena - OD 1700 RSD
      - `1-4-4-2` section - Proizvod 2
        - `1-4-4-2-1` image - Fotografija - (slika)
        - `1-4-4-2-2` text - Naziv - Proizvod
        - `1-4-4-2-3` text - Cena - OD 1700 RSD
      - `1-4-4-3` section - Proizvod 3
        - `1-4-4-3-1` image - Fotografija - (slika)
        - `1-4-4-3-2` text - Naziv - Proizvod
        - `1-4-4-3-3` text - Cena - OD 1700 RSD
    - `1-4-5` section - 4 proizvoda  **iskljuceno**
      - `1-4-5-1` section - Proizvod 1
        - `1-4-5-1-1` image - Fotografija - (slika)
        - `1-4-5-1-2` text - Naziv - Proizvod
        - `1-4-5-1-3` text - Cena - OD 1700 RSD
      - `1-4-5-2` section - Proizvod 2
        - `1-4-5-2-1` image - Fotografija - (slika)
        - `1-4-5-2-2` text - Naziv - Proizvod
        - `1-4-5-2-3` text - Cena - OD 1700 RSD
      - `1-4-5-3` section - Proizvod 3
        - `1-4-5-3-1` image - Fotografija - (slika)
        - `1-4-5-3-2` text - Naziv - Proizvod
        - `1-4-5-3-3` text - Cena - OD 1700 RSD
      - `1-4-5-4` section - Proizvod 4
        - `1-4-5-4-1` image - Fotografija - (slika)
        - `1-4-5-4-2` text - Naziv - Proizvod
        - `1-4-5-4-3` text - Cena - OD 1700 RSD
    - `1-4-2` section - 5 proizvoda
      - `1-4-2-1` section - Proizvod 1
        - `1-4-2-1-1` image - Fotografija - (slika)
        - `1-4-2-1-2` text - Naziv - Proizvod
        - `1-4-2-1-3` text - Cena - OD 1700 RSD
      - `1-4-2-2` section - Proizvod 2
        - `1-4-2-2-1` image - Fotografija - (slika)
        - `1-4-2-2-2` text - Naziv - Proizvod
        - `1-4-2-2-3` text - Cena - OD 1700 RSD
      - `1-4-2-3` section - Proizvod 3
        - `1-4-2-3-1` image - Fotografija - (slika)
        - `1-4-2-3-2` text - Naziv - Proizvod
        - `1-4-2-3-3` text - Cena - OD 1700 RSD
      - `1-4-2-4` section - Proizvod 4
        - `1-4-2-4-1` image - Fotografija - (slika)
        - `1-4-2-4-2` text - Naziv - Proizvod
        - `1-4-2-4-3` text - Cena - OD 1700 RSD
      - `1-4-2-5` section - Proizvod 5
        - `1-4-2-5-1` image - Fotografija - (slika)
        - `1-4-2-5-2` text - Naziv - Proizvod
        - `1-4-2-5-3` text - Cena - OD 1700 RSD
    - `1-4-6` section - 6 proizvoda  **iskljuceno**
      - `1-4-6-1` section - Proizvod 1
        - `1-4-6-1-1` image - Fotografija - (slika)
        - `1-4-6-1-2` text - Naziv - Proizvod
        - `1-4-6-1-3` text - Cena - OD 1700 RSD
      - `1-4-6-2` section - Proizvod 2
        - `1-4-6-2-1` image - Fotografija - (slika)
        - `1-4-6-2-2` text - Naziv - Proizvod
        - `1-4-6-2-3` text - Cena - OD 1700 RSD
      - `1-4-6-3` section - Proizvod 3
        - `1-4-6-3-1` image - Fotografija - (slika)
        - `1-4-6-3-2` text - Naziv - Proizvod
        - `1-4-6-3-3` text - Cena - OD 1700 RSD
      - `1-4-6-4` section - Proizvod 4
        - `1-4-6-4-1` image - Fotografija - (slika)
        - `1-4-6-4-2` text - Naziv - Proizvod
        - `1-4-6-4-3` text - Cena - OD 1700 RSD
      - `1-4-6-5` section - Proizvod 5
        - `1-4-6-5-1` image - Fotografija - (slika)
        - `1-4-6-5-2` text - Naziv - Proizvod
        - `1-4-6-5-3` text - Cena - OD 1700 RSD
      - `1-4-6-6` section - Proizvod 6
        - `1-4-6-6-1` image - Fotografija - (slika)
        - `1-4-6-6-2` text - Naziv - Proizvod
        - `1-4-6-6-3` text - Cena - OD 1700 RSD
    - `1-4-3` section - Pogledaj sve
      - `1-4-3-1` button - Dugme - Pogledaj sve
  - `1-5` section - Kupci
    - `1-5-1` section - Naslov sekcije
      - `1-5-1-1` text - Tekst - NAŠI KUPCI
    - `1-5-3` section - 3 kupca  **iskljuceno**
      - `1-5-3-1` image - Kupac 1 - (slika)
      - `1-5-3-2` image - Kupac 2 - (slika)
      - `1-5-3-3` image - Kupac 3 - (slika)
    - `1-5-4` section - 4 kupca  **iskljuceno**
      - `1-5-4-1` image - Kupac 1 - (slika)
      - `1-5-4-2` image - Kupac 2 - (slika)
      - `1-5-4-3` image - Kupac 3 - (slika)
      - `1-5-4-4` image - Kupac 4 - (slika)
    - `1-5-5` section - 5 kupaca  **iskljuceno**
      - `1-5-5-1` image - Kupac 1 - (slika)
      - `1-5-5-2` image - Kupac 2 - (slika)
      - `1-5-5-3` image - Kupac 3 - (slika)
      - `1-5-5-4` image - Kupac 4 - (slika)
      - `1-5-5-5` image - Kupac 5 - (slika)
    - `1-5-2` section - 6 kupaca
      - `1-5-2-1` image - Kupac 1 - (slika)
      - `1-5-2-2` image - Kupac 2 - (slika)
      - `1-5-2-3` image - Kupac 3 - (slika)
      - `1-5-2-4` image - Kupac 4 - (slika)
      - `1-5-2-5` image - Kupac 5 - (slika)
      - `1-5-2-6` image - Kupac 6 - (slika)
  - `1-6` section - Instagram
    - `1-6-1` section - Naslov sekcije
      - `1-6-1-1` text - Tekst - NAŠ INSTAGRAM
    - `1-6-2` section - Fotografije
      - `1-6-2-1` image - Fotografija 1 - (slika)
      - `1-6-2-2` image - Fotografija 2 - (slika)
      - `1-6-2-3` image - Fotografija 3 - (slika)
      - `1-6-2-4` image - Fotografija 4 - (slika)

## Varijante po broju stavki

Ukljucena je tacno jedna po sekciji; broj se menja gasenjem jedne i paljenjem druge.

| sekcija | 2 | 3 | 4 | 5 | 6 |
|---|---|---|---|---|---|
| kategorije | `1-2-3` | `1-2-2` | `1-2-4` | | `1-2-5` |
| proizvodi | | `1-4-4` | `1-4-5` | `1-4-2` | `1-4-6` |
| kupci | | `1-5-3` | `1-5-4` | `1-5-5` | `1-5-2` |
