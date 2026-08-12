# Produktvelger

En liten produktside med antallsvelger, faner/søk og direkte "legg i
handlekurv" mot shop.vodacom.no (Webmercs).

## Filstruktur

```
produktvelger/
├── index.html                 IKKE rediger - limet som binder alt sammen
├── config.js                  ✅ REDIGER - dataBaseUrl/imgBaseUrl (lokal vs. live)
├── data/
│   ├── produkter.csv          ✅ REDIGER - legg til/fjern produkter her
│   ├── telefoner.csv          ✅ REDIGER - telefonvarianter, se eget avsnitt under
│   └── innstillinger.json     ✅ REDIGER - tekster og gruppe-rekkefølge
├── bilder/                    ✅ REDIGER - telefon-produktbilder (.webp)
├── styles/
│   ├── theme.css               ✅ REDIGER - farger, fonter, avstander
│   ├── engine.css               ⚠️  IKKE rediger - strukturell styling
│   └── telefon-konfigurator.css ⚠️  IKKE rediger - telefonvelgerens egen stil
├── engine/
│   ├── pricing.js               ⚠️  IKKE rediger - prishenting (kort)
│   ├── cart.js                  ⚠️  IKKE rediger - handlekurv-logikk (kort)
│   ├── kontrast.js              ⚠️  IKKE rediger - WCAG-kontrastberegning
│   ├── telefon-konfigurator.js  ⚠️  IKKE rediger - telefonvelger-logikk
│   └── render.js                ⚠️  IKKE rediger - bygger siden
└── tests/                      Node-baserte enhetstester (valgfritt, se under)
```

✅ = trygt for alle å redigere
⚠️ = teknisk logikk - endringer her kan knekke siden

## Legge til et nytt produkt

Åpne `data/produkter.csv` og legg til en ny rad nederst:

```
Gruppe,Undergruppe,Tittel,Modell,Lenke,ProduktID,KategoriID
Datamaskiner,Bærbar,Kontor PC,Lenovo ThinkCentre M75,https://shop.vodacom.no/...,1006999999,36161
```

- **Gruppe**: hvilken fane produktet skal vises i. Skriver du inn et
  gruppenavn som ikke finnes fra før (f.eks. "Nettverksutstyr"),
  opprettes en helt ny fane automatisk - du trenger ikke røre noen kode.
  ("Telefon" er reservert til telefonvelgeren, se under - en CSV-rad
  med Gruppe=Telefon her vil bli overstyrt av telefonvelger-seksjonen.)
- **Undergruppe** (valgfri): deler produktene inne i én gruppe i
  underoverskrifter, f.eks. "Bærbar" og "Stasjonær" under
  "Datamaskiner". La feltet stå tomt for produkter som ikke trenger
  underinndeling (f.eks. skjermer). Nye undergruppenavn dukker
  automatisk opp som egne overskrifter, i den rekkefølgen de først
  opptrer i CSV-en - ingen kode å røre her heller.
- **Tittel**: kort betegnelse, vises øverst på kortet (f.eks. "Kontor PC").
- **Modell**: produktnavnet, vises under tittelen. Inneholder modellen
  et komma eller anførselstegn (f.eks. tommestørrelse i parentes),
  sett hele feltet i anførselstegn: `"Lenovo L14 (14"")"`.
- **Lenke**: full URL til produktsiden. Brukes både som fallback-lenke
  og til å hente prisen.
- **ProduktID**: tallet bakerst i produkt-URL-en (f.eks. `.../p1006822728`
  → `1006822728`). Mangler denne, virker kortet som en vanlig lenke til
  produktsiden i stedet for direkte kjøp.
- **KategoriID**: tallet i URL-en rett etter `cat-p/c` (f.eks.
  `cat-p/c36159/...` → `36159`). Vet du ikke hva den skal være, sett `0`.

## Telefonvelgeren ("Telefon"-fanen)

Telefonvelgeren fra det tidligere frittstående "Telefon Konfigurator"-
prosjektet er integrert som en egen fane, ikke som produktkort - den
beholder sin egen fire-stegs utforming (merke → modell → kapasitet →
farge).

**Legge til/endre telefonvarianter:** rediger `data/telefoner.csv`.
Kolonner:

```
Merke,Modell,Kapasitet,Farge,Fargekode,Lenke,Bilde
Samsung,Galaxy S26,256GB,Black,#2C2C2E,https://shop.vodacom.no/.../p1006442059,Samsung-Galaxy-S26.webp
```

- **Merke/Modell/Kapasitet/Farge**: påkrevde. En rad som mangler ett
  av disse hoppes over (varsel i konsollen), resten av widgeten
  påvirkes ikke.
- **Fargekode**: gyldig CSS-fargeverdi (HEX, f.eks. `#2C2C2E`) brukt
  som bakgrunn på fargeknappen. Tekstfargen på knappen (sort/hvit)
  beregnes automatisk med faktisk WCAG-kontrastberegning
  (`engine/kontrast.js`) - ikke en enkel terskel. Ugyldig eller
  manglende Fargekode gir en trygg fallback-grå i stedet for å
  ødelegge resten av telefonvelgeren.
- **Lenke** (ekstra felt utover de fem påkrevde): full URL til
  produktsiden - brukes til kjøp-i-handlekurv, "Se produktside" og
  prisvisning, akkurat som `Lenke` i `produkter.csv`.
- **Bilde** (ekstra felt): filnavn i `bilder/`-mappen for modellens
  produktbilde. Samme filnavn kan gå igjen på flere rader/modeller
  (f.eks. deler Galaxy S26 og S26+ bilde bevisst).

**Ekstrautstyr per telefonmodell** (Deksel, Lommebok, Skjermbeskytter,
Lader) er fortsatt definert som JS-data (`TILBEHOR`) øverst i
`engine/telefon-konfigurator.js`, siden det ikke er del av
CSV-kravet for telefonvariantene.

**Rekkefølge på Telefon-fanen** styres av samme
`gruppeRekkefolge`-liste i `data/innstillinger.json` som de andre
fanene.

## Endre rekkefølge på fanene

Åpne `data/innstillinger.json` og juster listen `gruppeRekkefolge`.
Grupper som ikke står der havner automatisk til slutt.

## Endre tekster på siden

`sidetittel`, `introTekst` og `sokPlaceholder` i
`data/innstillinger.json`.

## Justere farger/fonter

`styles/theme.css` inneholder alle fargene og fontene som en liste med
navngitte verdier (CSS-variabler) øverst i filen. Endre verdien, ikke
navnet på variabelen.

## Lokal testing

Denne siden bruker `fetch()` til å laste `data/`- og `bilder/`-filene,
så den må kjøres via en lokal webserver (ikke bare dobbeltklikkes):

```
# I mappen produktvelger/
python -m http.server 8000
```

Åpne deretter http://localhost:8000 i nettleseren.

**Husk:** `config.js` peker som standard på den *live*
GitHub Pages-adressen for både `dataBaseUrl` og `imgBaseUrl`. Sett
begge til `"./data/"` og `"./bilder/"` midlertidig for å teste mot
filene i denne mappen lokalt - se kommentaren i `config.js`.

**Merk:** "Legg i handlekurv", live pris og telefonvelgerens
"Kjøp"-knapp krever at siden kjører på shop.vodacom.no sitt eget
domene (samme prinsipp som telefon-konfiguratoren opprinnelig hadde -
limes inn som HTML-innhold i Webmercs-CMS). Ved lokal testing vises
"Se pris i butikk" i stedet for pris der en gyldig produktlenke
finnes, og klikk på et kort/kjøp-knapp åpner produktsiden i stedet
for å legge det i kurven direkte. Dette er forventet oppførsel, ikke
en feil.

## Tester

`tests/` inneholder enkle Node-baserte enhetstester (ingen
testrammeverk, ingen build-steg - i tråd med prosjektets
build-frie filosofi) for kontrastberegningen og CSV-valideringen:

```
npm install papaparse --no-save   # kun nødvendig for CSV-testen, brukes ikke i produksjon
node tests/kontrast.test.js
node tests/telefoner-csv.test.js
```

## Publisering

### 1. Push til GitHub

Last opp hele `Produktvelger/`-mappen (inkl. `bilder/` og `data/`) til
GitHub Pages-repoet. Push til hovedgrenen - det finnes ingen CI eller
staging, det du pusher er det som til slutt blir live.

### 2. Sett `config.js` til live-adressene

**Viktig, lett å glemme:** `config.js` peker som standard på den
*live* GitHub Pages-URL-en for `dataBaseUrl` og `imgBaseUrl` - de må
IKKE stå på `./data/`/`./bilder/` (det er kun for lokal testing på
localhost). Er de satt til relative stier når siden limes inn på
shop.vodacom.no, klarer den ikke å finne data-/bildefilene sine
(HTML-en kjører jo da fra `shop.vodacom.no` sitt domene, ikke fra
GitHub Pages). Dobbeltsjekk før publisering:

```js
window.PV_CONFIG = {
  dataBaseUrl: "https://<brukernavn>.github.io/Produktvelger/data/",
  imgBaseUrl: "https://<brukernavn>.github.io/Produktvelger/bilder/"
};
```

### 3. Vent på GitHub Pages-cachen

Endringer er normalt live innen ca. 10 minutter etter push (GitHub
Pages sin egen cache). Sjekk gjerne direkte på
`https://<brukernavn>.github.io/Produktvelger/` før du limer noe inn
i Webmercs, så du vet at siden i seg selv fungerer.

### 4. Lim inn i Webmercs

To måter å bygge den inn på, avhengig av hvordan siden skal sitte i
Webmercs-CMS-en:

**A) Direkte som HTML-innhold** (samme prinsipp som telefon-
konfiguratoren opprinnelig brukte) - limes rett inn i forsiden eller
en Info-seksjon i Webmercs sin editor:

```html
<div class="pv-container" id="pv-container-root"></div>
<link rel="stylesheet" href="https://<brukernavn>.github.io/Produktvelger/styles/theme.css">
<link rel="stylesheet" href="https://<brukernavn>.github.io/Produktvelger/styles/engine.css">
<link rel="stylesheet" href="https://<brukernavn>.github.io/Produktvelger/styles/telefon-konfigurator.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>
<script src="https://<brukernavn>.github.io/Produktvelger/config.js"></script>
<script src="https://<brukernavn>.github.io/Produktvelger/engine/pricing.js"></script>
<script src="https://<brukernavn>.github.io/Produktvelger/engine/cart.js"></script>
<script src="https://<brukernavn>.github.io/Produktvelger/engine/kontrast.js"></script>
<script src="https://<brukernavn>.github.io/Produktvelger/engine/telefon-konfigurator.js"></script>
<script src="https://<brukernavn>.github.io/Produktvelger/engine/render.js"></script>
```

Dette er den ANBEFALTE måten: siden kjører da direkte på
`shop.vodacom.no` sitt eget domene, som er en forutsetning for at
same-origin-avhengige funksjoner (live pris og "legg i handlekurv")
skal virke i det hele tatt - se punkt under.

**B) Som iframe** - enklere å lime inn, men "legg i handlekurv" og
live pris vil IKKE fungere (iframen kjører fortsatt på
`github.io`-domenet, ikke `shop.vodacom.no`, så same-origin-kravet
brytes). Bruk kun denne varianten hvis produktvelgeren skal fungere
som en ren "se produkter og klikk deg videre"-side:

```html
<iframe
  src="https://<brukernavn>.github.io/Produktvelger/index.html"
  style="width:100%; border:0; min-height:1200px;"
  title="Produktvelger">
</iframe>
```

### 5. Samme-origin-påminnelse

"Legg i handlekurv", "Kjøp"-knappen i telefonvelgeren og live pris er
alle avhengige av at siden faktisk kjører på `shop.vodacom.no` sitt
eget domene (alternativ A over) - ikke en iframe, ikke lokal testing.
Er dette ikke oppfylt, faller alt automatisk tilbake til lenker til
produktsiden i stedet ("Se pris i butikk" / åpne produktsiden) - det
er forventet oppførsel, ikke en feil, se "Lokal testing" over.