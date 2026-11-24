# Reisportaal Azië 2027

Interactieve versie van `Reisplanning_Azie_21feb_27mei_2027.xlsx`: planning per dag, to-do's, vluchten, kosten en tour-opties. Luuk en Vera werken in dezelfde data; wijzigingen zijn direct bij de ander zichtbaar. Geen login – wel een vraag "wie ben jij?" zodat je ziet wie wat heeft aangepast.

**Architectuur:** statische site op GitHub Pages, data in één Supabase-tabel (`portal_sections`). Zonder Supabase draait de site in lokale modus (opslag in je eigen browser).

## Bestanden

| Bestand | Wat |
|---|---|
| `index.html` | De hele site (één bestand, geen build-stap) |
| `config.js` | Supabase-URL en anon key – het enige bestand dat je moet invullen |
| `data.json` | Startdata uit de Excel; wordt bij de eerste keer laden automatisch in Supabase gezet |
| `supabase.sql` | Script dat de tabel + rechten aanmaakt |

## Installatie (±10 minuten)

### 1. Supabase
1. Maak op [supabase.com](https://supabase.com) een nieuw project (gratis tier is ruim genoeg) – of gebruik het project dat je al hebt voor je to-do-lijst.
2. Ga naar **SQL Editor → New query**, plak de inhoud van `supabase.sql` en klik **Run**.
3. Ga naar **Project Settings → API** en kopieer de **Project URL** en de **anon public** key.

### 2. config.js invullen
```js
window.PORTAL_CONFIG = {
  supabaseUrl: "https://xxxxxxxxxxxx.supabase.co",
  supabaseAnonKey: "eyJhbGciOi...",
};
```

### 3. GitHub Pages
1. Maak op GitHub een repo, bijvoorbeeld `kluukzii/azie-2027`, en zet alle bestanden uit deze map in de root (`index.html` moet bovenin staan).
2. **Settings → Pages → Build and deployment → Source: Deploy from a branch**, branch `main`, folder `/ (root)`. Opslaan.
3. Na een minuut staat de site op `https://kluukzii.github.io/azie-2027/`.

### 4. Eerste keer openen
Open de site. De tabel is nog leeg, dus de site zet `data.json` (de Excel-import) zelf in Supabase. Daarna is Supabase de bron van de waarheid; `data.json` wordt niet meer gebruikt, behalve als back-up.

Stuur Vera de link. Klaar.

## Goed om te weten

- **Beveiliging.** Er is bewust geen login. Iedereen die de link *én* de anon key kent (die staat in `config.js`, dus in de repo) kan de data lezen en aanpassen. Deel de link alleen met wie erbij mag. Wil je later toch een slotje? Dan is dat met Supabase Auth + een andere policy in `supabase.sql` toe te voegen zonder de site om te bouwen.
- **Back-up.** Instellingen → *Download back-up* geeft je een `data.json` met alles. *Herstel uit back-up* zet 'm terug (overschrijft alles).
- **Dagen toevoegen/verwijderen.** Open een dag in de planning → *Nacht toevoegen na deze dag* of *Verwijderen*. Datums en dagnummers van alle volgende dagen schuiven automatisch mee; de einddatum ook.
- **Vertrekdatum wijzigen** kan bij Instellingen; alle dagen schuiven mee.
- **Gelijktijdig bewerken.** Elke tab (planning, to-do's, …) wordt apart opgeslagen. Werken jullie precies tegelijk in dezelfde tab, dan wint de laatste die opslaat. In de praktijk gebeurt dat zelden; je ziet de wijziging van de ander binnen een seconde verschijnen.
- **Lokaal testen.** Open `index.html` niet rechtstreeks (dan mag de browser `data.json` niet laden), maar draai bijvoorbeeld `python3 -m http.server` in deze map en ga naar `http://localhost:8000`.

## Opnieuw importeren uit Excel

Wil je de Excel toch nog eens als startpunt gebruiken? `convert.py` (in deze map) maakt een nieuwe `data.json`:

```
python3 convert.py Reisplanning.xlsx data.json
```

Daarna in Supabase de tabel leegmaken (`delete from portal_sections;`) en de site opnieuw openen, of via Instellingen → *Herstel uit back-up* de nieuwe `data.json` inladen.
