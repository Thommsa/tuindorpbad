# Dagstaat Tuindorpbad

Eenvoudige webapp waarmee badmeesters en vrijwilligers van het **Tuindorpbad Hengelo** dagelijks een dagstaat kunnen invullen.

- Mobielvriendelijk (werkt goed op telefoon)
- Formulier + overzicht
- Gebaseerd op het papieren formulier *Afrekenstaat Kaartverkoop*
- Download naar Excel (CSV)
- Werkt lokaal (localStorage) én gedeeld via Supabase (cloud)

## Status

Supabase is al gekoppeld in `index.html` — de app slaat dagstaten direct op in de cloud, gedeeld voor iedereen met de link. Ook zonder internet werkt de app door: gegevens worden dan lokaal bewaard en later samengevoegd zodra de cloud weer bereikbaar is.

## Live testen

1. Open de file `index.html` in een browser, **of**
2. Ga naar de live versie op Vercel (zie onder bij Deployen).

## Velden (gebaseerd op papieren Afrekenstaat)

| Veld | Type |
|------|------|
| Datum | datum (standaard vandaag) |
| Naam | tekst (badmeester/assistent) |
| Temp | getal (°C watertemperatuur) |
| Eind Jeugd/65+ | getal (eindnummer kortingskaart) |
| Aantal Jeugd/65+ | getal |
| Eind Volwassenen | getal (eindnummer volwassenen) |
| Aantal Volwassenen | getal |
| Teller | getal |
| Totaal | getal (totaal bezoekers) |
| Dagstaat / Bijzonderheden | groot tekstveld |

## Supabase — database-structuur

Dit is de structuur die momenteel in het gekoppelde Supabase-project staat. Alleen nodig als je de tabel ooit opnieuw moet aanmaken (bijv. nieuw project of key gerouteerd):

```sql
create table dagstaten (
  id uuid primary key default gen_random_uuid(),
  datum date not null,
  naam text not null,
  temp numeric,
  eind_jeugd integer,
  aantal_jeugd integer,
  eind_volwassenen integer,
  aantal_volwassenen integer,
  teller integer,
  totaal integer,
  dagstaat text,
  created_at timestamptz default now(),
  constraint totaal_niet_negatief check (totaal is null or totaal >= 0)
);

alter table dagstaten enable row level security;

-- Iedereen mag lezen (simpel, open link — geen login nodig)
create policy "Iedereen mag lezen"
  on dagstaten for select
  using (true);

-- Iedereen mag invoegen, met minimale validatie zodat lege/onzinnige
-- rijen niet geaccepteerd worden
create policy "Iedereen mag invoegen (gevalideerd)"
  on dagstaten for insert
  with check (
    datum is not null
    and naam is not null and length(trim(naam)) > 0
    and (totaal is null or totaal >= 0)
    and (teller is null or teller >= 0)
  );
```

Als je ooit naar een nieuw Supabase-project moet overstappen:

1. Maak een nieuw project op [supabase.com](https://supabase.com)
2. Voer bovenstaande SQL uit in de **SQL Editor**
3. Ga naar **Project Settings → API Keys** en kopieer de **Project URL** en de **publishable key**
4. Vul die in bovenaan in de `<script>`-sectie van `index.html`:

```js
const SUPABASE_URL = 'https://jouw-project.supabase.co';
const SUPABASE_ANON_KEY = 'jouw-publishable-key';
```

5. Commit en push — Vercel deployt automatisch opnieuw.

> **Beveiliging:** er is geen authenticatie op deze tabel — iedereen met de publishable key (zichtbaar in `index.html`, ook op GitHub) kan lezen en geldige rijen toevoegen. Dat is bewust simpel gehouden. Merk je spam op? Rotate de key via **Project Settings → API → API Keys**.

## Deployen (gratis)

### Vercel
1. Ga naar [vercel.com](https://vercel.com) en log in met GitHub
2. Import de repository `tuindorpbad`
3. Deploy (geen build-instellingen nodig — dit is een losse statische `index.html`)
4. Elke push naar `main` deployt automatisch opnieuw

### Alternatief — GitHub Pages
1. Repo Settings → Pages
2. Source: Deploy from a branch → `main` / root
3. Na een minuut staat de app op `https://thommsa.github.io/tuindorpbad/`

## Broncode

https://github.com/Thommsa/tuindorpbad
