# Hostingstandard

| Felt | Værdi |
| --- | --- |
| Owner | Niclas Nielsen |
| Status | Udkast |
| Last verified | 2026-10-21 |
| Review trigger | Nyt system, der ikke passer i et niveau. Når Hetzner-opskriften er afprøvet. Ændring i pris eller krav hos en udbyder |

Hvor et system hostes, afgøres af, hvor kritisk det er, og hvem der bruger det, ikke af en fælles regel for alt. Der er fire niveauer.

## Niveauer

| Niveau | Bruges til | Eksempler |
| --- | --- | --- |
| Azure | Kundevendte eller kritiske systemer, hvor managed backup med point-in-time restore, managed database og Key Vault er pengene værd | Thought-Leadership |
| Hetzner Cloud | Interne systemer, hvor nedetid på op til en arbejdsdag kan accepteres, og hvor de faste Azure-omkostninger ikke står mål med værdien | Primetime Vidensbank. Resource-Booking-System (planlagt flytning fra Azure) |
| Netlify | Statiske sider og simple apps uden egen database og uden følsomme data | Billedgenerator, formular til Google Sheets |
| Kundens egen hosting | Systemer, vi leverer til en kunde, som selv driver dem | Kundens kopi af vidensbanken |

Er et system i tvivl mellem Azure og Hetzner, vælges Azure, indtil der er en begrundelse for andet.

## Krav på Hetzner-niveauet

På Hetzner overtager vi selv det, Azure ellers gør for os. Derfor gælder:

- **Servere:** Hetzner Cloud (virtuelle servere) i en EU-lokation. Ikke auktionsservere som standard, fordi de ikke kan udvides eller tages snapshot af, og fordi vi selv skal håndtere hardwarefejl.
- **Adskillelse:** systemer med kundemateriale, persondata i større mængder eller bred adgang til Microsoft 365 eller SharePoint får deres egen server. Mindre følsomme interne systemer må dele en server.
- **Databehandleraftale:** aftalen med Hetzner skal være på plads, før der ligger persondata på serveren, og Hetzner skal stå på projektets liste over databehandlere.
- **Opsætning:** Docker Compose, Caddy foran med automatisk TLS og Postgres som database, medmindre projektets ADR siger andet.
- **Backup:** dagligt krypteret backup til lagring uden for serveren i EU, med en opbevaringstid, der står i projektets charter. Gendannelse skal være afprøvet, før systemet går i drift.
- **Overvågning:** alarmer fra en tjeneste uden for serveren, så nedetid opdages, selvom serveren er helt nede.
- **Hærdning:** firewall med kun de nødvendige porte åbne, SSH kun med nøgle og automatiske sikkerhedsopdateringer af styresystemet.
- **Hemmeligheder:** ligger aldrig i git, heller ikke i deploy-scripts.
- **Login:** sker i selve appen mod Entra ID, ikke via en platformfunktion som Azure Easy Auth.

## Flytbarhed

Systemer, der kan blive leveret til en kunde, skal kunne flyttes uden at hænge fast i en bestemt udbyder:

- Brug standardkomponenter, hvor de rækker, fx Postgres som jobkø frem for en udbyders egen kø.
- Tenant-ID, domæne og andre installationsspecifikke værdier er konfiguration og står ikke i koden.
- Hele systemet skal kunne startes med containere og en dokumenteret opsætning.

## Proces

- Projektets charter angiver niveauet, og projektets hosting-ADR begrunder det med henvisning til denne standard.
- Et skift af niveau, fx fra Azure til Hetzner, kræver en ADR i projektet.
- Hetzner-niveauet er ikke afprøvet endnu. Vidensbanken er det første system, og den konkrete opskrift (opsætning, backup, overvågning, deploy) skrives ind her, når den har vist sig at holde. Andre systemer flyttes først derefter.
