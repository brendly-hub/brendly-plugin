---
name: brendly-novac
description: Brojke i novac u Brendly prodavnici preko MCP-a - promet i zarada po danu i mesecu, najprodavaniji proizvodi, marže, porudžbine i njihovi filteri, povrati i reklamacije, popusti i promo kodovi kao izveštaj, stanje računa i zahtev za isplatu sa fakturom. Koristi za "koliko sam prodao", "šta mi se najbolje prodaje", "koliko zaradim po majici", "pokaži porudžbine", "koliko korpi je napušteno", "koliko mogu da podignem", "podigni mi novac", "da li mi se popust isplati".
---

# Brendly: brojke i novac

Sve je čitanje, osim zahteva za isplatu. Porudžbine se ne menjaju kroz MCP.

## Odakle koji broj dolazi

| Pitanje | Poziv |
|---|---|
| promet, zarada, prosečna korpa po periodu | `brendly_analytics by_month` ili `by_day` |
| najprodavaniji proizvodi, zarada po proizvodu | `brendly_analytics product_sales` |
| učinak popusta i promo kodova | `brendly_analytics promo_report` |
| marža celog kataloga po tipu i boji | `brendly_products_bulk margin_summary` |
| porudžbine, sadržaj, stanja, kupci | `brendly_orders list` |
| povrati i reklamacije | `brendly_returned_products`, razlog je u punom zapisu porudžbine |
| napuštene korpe | `brendly_abandoned_checkouts_read` |
| stanje računa i dug | `brendly_shop_read balance` / `debt` |
| raspoloživo za isplatu, istorija, načini | `brendly_payouts_read` |

**Nula je odgovor.** Prodavnica bez prometa vraća nule i prazne liste; to saopštavam kao
činjenicu, ne kao grešku i ne kao „ne mogu da pročitam".

## Zamke koje menjaju odgovor

- **Prodavnica bez načina korišćenja tiho vraća nule** iz `by_month`, `by_day` i
  `product_sales`, i kad prometa ima. Prvo `shop_settings_read get_platform_usage_mode`; ako je
  prazan, to je uzrok i kažem ga, ne izveštavam nule kao stvarno stanje.
- **Valuta.** `balance`, `debt`, `available_amount` i `promo_report` nose `valuta`. `by_day`,
  `by_month` i `product_sales` je ne nose: valuta je valuta tržišta prodavnice (RS dinar, HR, BA
  i EU evro) i tako je i navodim. Iznosi sa različitih tržišta se ne sabiraju.
- **Razrada po boji, veličini i kategoriji** ne postoji kao izveštaj. Računa se iz
  `orders list full:true`, gde svaka stavka nosi `productColor`, `productSize`, `orderItemPrice`,
  `basePrice`, `discount` i `promoCode`. Kažem korisniku da je to izračunato iz porudžbina.
- **Trošak.** `basePrice` na stavci je nabavna strana. Porez, provizija i poštarina nisu izloženi,
  pa „koliko mi ostaje u džepu" računam do nivoa marže i to jasno kažem.
- **Datum isporuke** nije filter. `orders list` filtrira po datumu nastanka; kad treba isporuka,
  ide `states:["DELIVERED"]` pa `transitions` po porudžbini.
- **Povrat ne nosi iznos.** Vidi se šta je vraćeno i zašto, ne i koliko je koštalo.
- **Fakture** nemaju svoju listu. Adrese su polja `invoice` i `buyerInvoice` u punom zapisu
  porudžbine.

## Popusti i kodovi

Oblik tela pri kreiranju, isti kao u platformi:

```
discounts_write create   data: {name, label?, type: "PERCENT"|"FIXED", value, endDate, productIdList}
promo_codes_write create data: {name, label?, type: "PERCENT"|"FIXED", appliesTo: "PRODUCTS"|"CART",
                                value, timesToBeUsed, endDate, productIdList?}
```

- `type` je samo `PERCENT` ili `FIXED`, velikim slovima; `value` je procenat ili iznos u valuti
  prodavnice. Bez `type` backend vraća 500 „Name is null" iako je naziv poslat: ta poruka znači
  da fali `type`, ne naziv.
- Procenat preko 100 platforma ne nudi; ne pravim ga ni kroz MCP.
- Popust traži bar jedan proizvod u `productIdList`. Kod sa `appliesTo: "CART"` važi na celu
  korpu i lista proizvoda se ne koristi; sa `PRODUCTS` (podrazumevano) lista je obavezna.
- `timesToBeUsed` je najmanje 1. `name` promo koda je tekst koji kupac kuca na kasi.
- Datumi su **UTC**, oblik `yyyy-MM-dd-HH-mm-ss`. Ponoć po Beogradu leti je `...-22-00-00`
  prethodnog dana.
- `active` znači uključen, ne „važi danas"; to kaže `vaziSada` u listi.
- Popust i promo kod **vezani za iste proizvode se isključuju**: platforma vraća
  `ACTION_OVERLAPPING` i imenuje akciju koja smeta. Kod na korpu je izuzetak i sme da ide ispod
  bazne cene; to je namerno poslovno pravilo i ne prijavljujem ga kao grešku.
- Popust na kategoriju je **snimak liste proizvoda** u trenutku pravljenja: proizvod dodat u
  kategoriju kasnije neće biti na akciji. Kažem to kad korisnik pravi takvu akciju.
- `promo_report` grupiše po kodu; popusti bez koda padaju u jedan red. Kad treba po akciji,
  računam iz porudžbina.

## Isplata

Ide samo u marketplace modelu, gde je Brendly prodavac i izdavalac računa.

```
payouts_read available_amount   -> koliko se može podići, sa valutom
payouts_read withdraw_options   -> načini; invoiceRequired kaže da li traži fakturu
payouts_write create_request    -> zahtev, confirm: true
payouts_write upload_invoice    -> faktura naknadno
```

Dva načina:

- **Native Teams** (online novčanik): faktura nije potrebna, zahtev je gotov jednim pozivom.
- **Račun firme**: faktura je obavezna. Zahtev se svejedno pravi (platforma i sama nudi „okači
  kasnije"), a odgovor nosi iznos, podatke Brendly firme kojoj se faktura izdaje i tačna pravila.
  Tada korisniku kažem šta faktura mora da sadrži, zamolim ga da je napravi i pošalje, pa je
  priložim kroz `upload_invoice`. Ne ostavljam ga sa „treba faktura".

Faktura mora da sadrži datum prometa, naziv i adresu firme, PIB i matični broj; predmet nabavke
je „Usluga posredovanja u prodaji", količina 1, cena jednaka iznosu isplate. Ako firma nije u
PDV-u, ide klauzula po članu 33; ako se ne koristi pečat, klauzula po članu 25. Neispravna
faktura se odbija bez objašnjenja, pa je bolje proveriti unapred nego posle.

## Šta ovde ne postoji

Pregledi proizvoda i posete se ne mere (to je u prodavčevom GA nalogu), proseka platforme nema,
trošak povrata nije izložen, roka sledeće isplate nema, a kompenzacija se više ne koristi.
Kad korisnik pita nešto od toga, kažem da podatak ne postoji u sistemu umesto da procenjujem.
