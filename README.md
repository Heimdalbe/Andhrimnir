# Andhrimnir

LAN-Catering project Heimdal

Deze repository bevat:

- **`backend/`**: ASP.NET Core API (.NET 9, EF Core, MySQL via Docker Compose)
- **`frontend/`**: React + Vite + TypeScript + Tailwind CSS + shadcn/ui

Deze README beschrijft hoe je het project **lokaal draaiend krijgt** nadat je het gecloned hebt.

---

## Inhoud

1. [Vereisten](#1-vereisten)
2. [Repository clonen](#2-repository-clonen)
3. [Backend starten](#3-backend-starten)
4. [Frontend starten](#4-frontend-starten)
5. [Tests en linting](#5-tests-en-linting)
6. [Handige commando's](#6-handige-commandos)
7. [Projectstructuur](#7-projectstructuur)
8. [Werkafspraken](#8-werkafspraken)
9. [Verantwoordelijken](#9-verantwoordelijken)
10. [Problemen oplossen](#10-problemen-oplossen)

---

## 1. Vereisten

Installeer vooraf:

| Tool                                                              | Versie                                                                           | Waarvoor                          |
| ----------------------------------------------------------------- | -------------------------------------------------------------------------------- | --------------------------------- |
| [Git](https://git-scm.com/)                                       | recent                                                                           | versiebeheer                      |
| [.NET SDK](https://dotnet.microsoft.com/download)                 | **9**                                                                            | backend                           |
| [Docker Desktop](https://www.docker.com/products/docker-desktop/) | met Docker Compose                                                               | MySQL-database                    |
| [Node.js](https://nodejs.org/en/)                                 | 26.\*                                                                            | frontend                          |
| [pnpm](https://pnpm.io/)                                          | recent                                                                           | package manager frontend          |
| IDE                                                               | Zelf te kiezen welke, Prefered: VScode voor frontend, Visual Studio voor backend | bv. VS Code, Rider, Visual Studio |

Aanbevolen VS Code-extensies voor de frontend: **ESLint** en **Tailwind CSS IntelliSense**.

Controleer je installatie:

```bash
git --version
dotnet --version        # moet 9.x zijn
docker --version
docker compose version
node -v
pnpm -v
```

Installeer ook de EF Core CLI-tool (eenmalig, nodig voor databasemigraties):

```bash
dotnet tool install --global dotnet-ef --version 9.*
```

Staat de tool al op je machine, dan volstaat:

```bash
dotnet tool update --global dotnet-ef --version 9.*
```

---

## 2. Repository clonen

```bash
git clone https://github.com/Heimdalbe/Andhrimnir
cd Andhrimnir
```

---

## 3. Backend starten

Alle commando's in dit hoofdstuk voer je uit vanuit de map `backend/`:

```bash
cd backend
```

### 3.1 Database starten (Docker)

Zorg dat Docker Desktop draait en start MySQL:

```bash
docker compose up -d
docker compose ps
```

De eerste keer duurt het enkele seconden voor MySQL klaar is. Volg de opstart met:

```bash
docker compose logs -f mysql
```

Wacht tot je een melding ziet dat de server _ready for connections_ is.

### 3.2 `.env` aanmaken

De connection string staat **niet** in Git. Maak lokaal je eigen `.env` aan in `src/Andhrimnir.Server/.env` op basis van het voorbeeld:

```env
DB_CONNECTION=Server=localhost;Port=3306;Database=AndhrimnirDbDEV;User=root;Password=root;
```

> ⚠️ Commit `.env` nooit. Het staat in `.gitignore`.

### 3.3 Packages herstellen en bouwen

```bash
dotnet restore
dotnet build
```

### 3.4 Databaseschema aanmaken (migrations)

Migrations worden **niet** in Git bewaard (`Migrations/` staat in `.gitignore`, ze zijn gegenereerd en
veroorzaken anders merge-conflicten). Elke developer maakt ze dus lokaal aan op basis van de huidige
entities. Bij de eerste setup:

```bash
dotnet ef migrations add InitialCreate --startup-project src/Andhrimnir.Server --project src/Andhrimnir.Persistence
dotnet ef database update --startup-project src/Andhrimnir.Server --project src/Andhrimnir.Persistence
```

**Na een `git pull` waarin entities of configuraties gewijzigd zijn**, pas je je lokale database aan met
een nieuwe migration:

```bash
dotnet ef migrations add <KorteBeschrijving> --startup-project src/Andhrimnir.Server --project src/Andhrimnir.Persistence
dotnet ef database update --startup-project src/Andhrimnir.Server --project src/Andhrimnir.Persistence
```

Loopt je lokale migrationgeschiedenis in de knoop, dan kan je altijd van nul herbeginnen, zie
[Problemen oplossen](#9-problemen-oplossen).

### 3.5 API starten

```bash
dotnet run --project src/Andhrimnir.Server
```

In de console zie je op welke URL de API draait (de poort staat ook in
`src/Andhrimnir.Server/Properties/launchSettings.json`). Open die URL in je browser: je zou
`API is running` moeten zien.

---

## 4. Frontend starten

Alle commando's in dit hoofdstuk voer je uit vanuit de map `frontend/`:

```bash
cd frontend
pnpm install
pnpm dev
```

De app draait meestal op <http://localhost:5173/>.

Andere nuttige commando's:

```bash
pnpm build      # productiebuild (map dist/)
pnpm lint       # code controleren met ESLint (vscode kan dit automatisch)
pnpm lint --fix # automatisch herstellen
```

---

## 5. Tests en linting

### (MOMENTEEL ZIT ER NIKS VAN TESTING IN DE REPO, DIT IS PAS VOOR LATER)

**Backend** (vanuit `backend/`):

```bash
dotnet test
```

Integratietests die een database nodig hebben, gebruiken een aparte testdatabase. Laat tests nooit je
ontwikkelingsdata aanpassen.

**Frontend** (vanuit `frontend/`):

```bash
pnpm lint
```

Voer `pnpm lint` en `dotnet test` uit **vóór elke commit**.

---

## 6. Handige commando's

### Backend (vanuit `backend/`)

| Commando                                                                                                       | Wat het doet                               |
| -------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| `docker compose up -d`                                                                                         | Start de MySQL-container                   |
| `docker compose down`                                                                                          | Stopt de container (data blijft)           |
| `docker compose down -v`                                                                                       | Stopt de container **en wist de database** |
| `dotnet build`                                                                                                 | Compileert de solution                     |
| `dotnet test`                                                                                                  | Voert alle tests uit                       |
| `dotnet run --project src/Andhrimnir.Server`                                                                   | Start de API                               |
| `dotnet ef migrations add <Naam> --startup-project src/Andhrimnir.Server --project src/Andhrimnir.Persistence` | Maakt een nieuwe migration aan             |
| `dotnet ef database update --startup-project src/Andhrimnir.Server --project src/Andhrimnir.Persistence`       | Voert migrations uit op de database        |

### Frontend (vanuit `frontend/`)

| Commando                        | Wat het doet                                 |
| ------------------------------- | -------------------------------------------- |
| `pnpm install`                  | Installeert alle dependencies                |
| `pnpm dev`                      | Start de development server (localhost:5173) |
| `pnpm build`                    | Bouwt de app voor productie                  |
| `pnpm lint` / `pnpm lint --fix` | Controleert / herstelt code met ESLint       |
| `pnpm add <package>`            | Voegt een dependency toe                     |
| `pnpm add -D <package>`         | Voegt een devDependency toe                  |

---

## 7. Projectstructuur

```text
Andhrimnir/
├── backend/
│   ├── Andhrimnir.sln
│   ├── compose.yaml                  # MySQL via Docker
│   ├── src/
│   │   ├── Andhrimnir.Domain/        # entities en businessregels
│   │   ├── Andhrimnir.Services/      # use-cases, applicatielogica, validatie
│   │   ├── Andhrimnir.Persistence/   # DbContext, configuraties (Migrations/ is lokaal, niet in Git)
│   │   └── Andhrimnir.Server/        # ASP.NET Core API, Program.cs, .env
│   └── tests/
│       ├── Andhrimnir.Domain.Tests/
│       ├── Andhrimnir.Services.Tests/
│       └── Andhrimnir.Integration.Tests/
├── frontend/
│   ├── public/                       # statische bestanden
│   ├── src/
│   │   ├── components/
│   │   ├── App.tsx
│   │   ├── index.css                 # globale stijl + Tailwind
│   │   ├── main.tsx                  # entry point
│   │   └── types.ts                  # gedeelde TypeScript-interfaces
│   ├── eslint.config.js
│   ├── vite.config.ts
│   └── package.json
├── .gitignore
└── README.md
```

**Afhankelijkheden in de backend:**

```text
Services    ──> Domain
Persistence ──> Domain
Server      ──> Services
Server      ──> Persistence
```

`Domain` kent de andere projecten niet. `Services` en `Persistence` verwijzen niet naar `Server`.

---

## 8. Werkafspraken

- **Geen geheimen in Git.** `.env`-bestanden worden genegeerd. Pas je de structuur van het `.env` aan,
  update dan ook de `README.md` (zonder echte waarden, indien relevant).
- **Migrations niet committen.** Ze staan in `.gitignore` en zijn per developer lokaal. Een migration
  aanmaken wijzigt de database nog niet; dat doet `dotnet ef database update`. Pull je wijzigingen aan
  entities van een collega, maak dan lokaal een nieuwe migration aan (zie 3.4).
- **`pnpm-lock.yaml` altijd mee committen.**
- **Domain blijft schoon.** Geen EF Core- of MySQL-specifieke configuratie in `Domain`; dat hoort in
  `Persistence`.
- Lint en test vóór je commit en push.
- PR's worden altijd gemaakt naar de DEV-branch, en deze kunnen **enkel** gemerged worden wanner Wout Lesschaeve (ICT) of Seppe Landtsheer (Pro-Senior) deze hebben goedgekeurd.
- Mergen naar de PROD branch gebeurd **enkel** door Wout of Seppe.

Vragen, opmerkingen, commentaar of wil je wat anders kwijt? Neem gerust contact op met Wout of Seppe, zij zullen je met veel plezier verder helpen :)

---

## 9. Verantwoordelijken

- Wout Lesschaeve - @wotsy op Discord
- Seppe Landtsheer - @zepje op Discord

## 10. Problemen oplossen

### note: dit hele stuk is ai generated dus als het (alsnog) niet werkt stuur ons gerust

**`DB_CONNECTION is not configured` bij het starten van de API**
Het `.env`-bestand ontbreekt of wordt niet gevonden. Controleer of
`backend/src/Andhrimnir.Server/.env` bestaat en dat de variabele `DB_CONNECTION` op één regel staat.
Start je de API vanuit een andere map dan `Andhrimnir.Server`, dan kan `Env.Load()` het bestand missen;
start dan eens vanuit `backend/src/Andhrimnir.Server` met `dotnet run`.

**Kan geen verbinding maken met MySQL**

- Draait Docker Desktop? Controleer met `docker compose ps`.
- Is de container net gestart? Wacht tot `docker compose logs mysql` _ready for connections_ toont.
- Draait er lokaal al een andere MySQL op poort 3306? Stop die of pas de poort aan in `compose.yaml` en
  in je `.env`.

**`dotnet ef` wordt niet herkend**
Installeer de tool: `dotnet tool install --global dotnet-ef --version 9.*`. Open daarna een nieuwe terminal.

**`dotnet ef` klaagt over versies of kan het project niet laden**
Controleer of je .NET SDK 9 gebruikt (`dotnet --version`) en voer de commando's uit vanuit `backend/` met
beide parameters `--startup-project` en `--project`.

**De database of mijn lokale migrations zijn in de knoop en ik wil van nul herbeginnen**

Verwijder de lokale migrations en de database, en maak ze opnieuw aan:

```bash
# 1. lokale migrations verwijderen (note, je kan dit ook gw handmatig verwijderen)
#    macOS / Linux / Git Bash:
rm -rf src/Andhrimnir.Persistence/Migrations
#    Windows PowerShell:
Remove-Item -Recurse -Force src/Andhrimnir.Persistence/Migrations

# 2. database wissen en opnieuw starten
docker compose down -v
docker compose up -d

# 3. migration opnieuw aanmaken en uitvoeren
dotnet ef migrations add InitialCreate --startup-project src/Andhrimnir.Server --project src/Andhrimnir.Persistence
dotnet ef database update --startup-project src/Andhrimnir.Server --project src/Andhrimnir.Persistence
```

⚠️ Dit verwijdert al je lokale databasegegevens.

**`database update` doet niets / tabellen ontbreken na het clonen**
Migrations zitten niet in Git. Maak eerst lokaal een migration aan met
`dotnet ef migrations add InitialCreate ...` (zie 3.4) en voer daarna `database update` uit.

**`pnpm` wordt niet herkend**
Installeer pnpm volgens <https://pnpm.io/installation> en open een nieuwe terminal.

**Poort 5173 is bezet**
Vite kiest automatisch een andere poort; kijk in de terminal welke.

**Imports met `@/` worden rood in de editor**
Herstart de TypeScript-server (VS Code: `F1` > _TypeScript: Restart TS Server_) en controleer of de
`paths`-instelling in zowel `tsconfig.json` als `tsconfig.app.json` staat.

---

## Bronnen

- [.NET](https://dotnet.microsoft.com/) · [EF Core](https://learn.microsoft.com/ef/core/) · [FastEndpoints](https://fast-endpoints.com/)
- [Vite](https://vite.dev/guide/) · [React](https://react.dev/) · [Tailwind CSS](https://tailwindcss.com/docs)
