# Dagstaat Tuindorpbad

Eenvoudige webapp waarmee badmeesters en vrijwilligers van het **Tuindorpbad Hengelo** dagelijks een dagstaat kunnen invullen.

- Mobielvriendelijk (werkt goed op telefoon)
- Formulier + overzicht
- Ticketnummers (begin/eind) → aantal kaartjes wordt automatisch berekend
- Download naar Excel (CSV)
- Werkt meteen lokaal (localStorage)
- Klaar om te koppelen aan gratis Supabase (cloud + gedeeld voor iedereen)

## Live testen

1. Open de file `index.html` in een browser, **of**
2. Zet de repo live via GitHub Pages of Vercel (zie onder).

## Velden (v1 – kunnen later aangepast worden)

| Veld | Type |
|------|------|
| Datum | datum (standaard vandaag) |
| Naam | tekst |
| Beginnummer | getal (ticket) |
| Eindnummer | getal (ticket) |
| Aantal kaartjes | automatisch berekend |
| Abonnementen jeugd | getal |
| Abonnementen 65+ | getal |
| Watertemperatuur | getal (°C) |
| Totaal bezoekers | getal |
| Dagstaat | groot tekstveld |

## Supabase koppelen (aanbevolen – 5 minuten)

1. Maak een gratis account op [supabase.com](https://supabase.com)
2. Maak een nieuw project
3. Ga naar **SQL Editor** en voer dit uit:

```sql
create table dagstaten (
  id uuid primary key default gen_random_uuid(),
  datum date not null,
  naam text not null,
  beginnummer integer,
  eindnummer integer,
  aantal_kaartjes integer,
  abonnement_jeugd integer default 0,
  abonnement_senior integer default 0,
  watertemp numeric,
  totaal_bezoekers integer,
  dagstaat text,
  created_at timestamptz default now()
);

-- Iedereen mag lezen en schrijven (simpel, open link)
alter table dagstaten enable row level security;

create policy "Iedereen mag lezen"
  on dagstaten for select
  using (true);

create policy "Iedereen mag invoegen"
  on dagstaten for insert
  with check (true);
```

4. Ga naar **Project Settings → API**
5. Kopieer de **Project URL** en de **anon public** key
6. Open `index.html` en vul bovenaan in de `<script>` sectie in:

```js
const SUPABASE_URL = 'https://jouw-project.supabase.co';
const SUPABASE_ANON_KEY = 'jouw-anon-key';
```

7. Sla op en deploy opnieuw. Iedereen met de link deelt nu dezelfde data.

## Deployen (gratis)

### Optie A – GitHub Pages
1. Repo Settings → Pages
2. Source: Deploy from a branch → `main` / root
3. Na een minuut staat de app op `https://thommsa.github.io/tuindorpbad/`

### Optie B – Vercel (aanbevolen)
1. Ga naar [vercel.com](https://vercel.com) en log in met GitHub
2. Import de repository `tuindorpbad`
3. Deploy (geen build-instellingen nodig)
4. Je krijgt een link zoals `https://tuindorpbad.vercel.app`

## Later aanpassen van velden

Alles staat in één bestand (`index.html`).  
Wil je velden toevoegen of weghalen? Zeg het, dan pas ik het aan of je kunt zelf de formuliervelden en de JavaScript-objecten wijzigen.

## Broncode

https://github.com/Thommsa/tuindorpbad
