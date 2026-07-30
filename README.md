# JALPA

Sisäinen web-sovellus **YT JalPa** -salibandyjoukkueelle. Korvasi aiemman React Native / Android -prototyypin. Käyttö on tarkoitettu vain joukkueen pelaajille ja hallinnolle — ei julkinen palvelu.

PWA: sovelluksen voi asentaa puhelimen aloitusnäytölle. Ulkoasu: tumma teema ja seuran keltainen (#FFD100).

## Ominaisuudet

| Osa | Kuvaus |
|-----|--------|
| **Kirjautuminen** | Pelaajanumero + nimi + salasana. Uusi pelaaja kirjautuu ensin tyhjällä salasanalla ja asettaa oman. |
| **Etusivu** | Omat pisteet, avoimet sakot, joukkueen saldo, IR-tilanne, tulevat tapahtumat. |
| **Nimenhuuto** | Harjoitukset ja pelit; toistuvat viikkotapahtumat. Ilmoittautuminen *Tulen* / *En tule*. |
| **Pelaajat** | NHL-henkiset **pelaajakortit** (KOK, tilastot, erikoiskortit). |
| **Pistepörssi** | Maalit, syötöt, pisteet (M+S), jäähyminuutit kaudella. |
| **Sakkokassa** | Sakot, maksutila, kausikohtainen seuranta. |
| **Laskut** | Kausimaksut pelaajille, joukkueen menot, MobilePay-linkit, maksujen merkintä. |
| **IR-lista** | Aktiiviset loukkaantumiset ja palautumisarviot. |
| **Hallinta** (admin) | Roster, pelaajien lisäys, sakot, pisteet, tapahtumat, laskutus. |

### Pelaajakortit

- **KOK** (kokonaisarvo) alkaa kaudella **75**.
- Nousee: +1 / maali, +1 / syöttö, +1 / osallistuttu harjoitus (nimenhuuto).
- Laskee: −1 / 2 jäähyminuuttia.
- Kortilla näkyvät suoraan: **LAUKAUS**, **SYÖTTÖ**, **HARJ**, **JÄÄHYT** (pistepörssi + nimenhuuto). **PUOL** tulossa myöhemmin.
- **Erikoiskortit:** *MAALIPUTKI* (≥2 peräkkäistä pelimerkintää maaleilla), *TREENIPUTKI* (≥10 peräkkäistä harjoitusta). Harjoitus tunnistetaan tapahtuman otsikosta (*harjoitus*, *treeni*, *training*).

Aktiivinen kausi on määritelty sekä frontendissä (`frontend/src/api.js` → `SEASON`) että backend-reiteissä (`CURRENT_SEASON`).

## Teknologiat

| Kerros | Stack |
|--------|--------|
| Frontend | React 18, React Router, Vite |
| Backend | Node.js, Express |
| Tietokanta | PostgreSQL 16 (Docker) |
| Auth | JWT, bcrypt |
| Muuta | QR-koodit (MobilePay), PWA (manifest + service worker) |

## Projektirakenne

```
jalpa/
├── backend/
│   src/
│   │   db/          migrate.js, pool.js, seed.js
│   │   lib/         roster.js, playerCards.js
│   │   middleware/  auth.js
│   │   routes/      auth, events, fines, points, invoices, injuries, playerCards
│   └── .env.example
├── frontend/
│   src/             sivut, komponentit, tyylit
│   public/          manifest, sw.js
│   dist/            tuotantobuild (generoidaan)
├── logo.jpg         palvellaan API:sta /logo.jpg
├── docker-compose.yml
├── PAIVITYS.md          git-pohjainen tuotantopäivitys
└── PAIVITYS-WINSCP.md   WinSCP + paikallinen build
```

## Kehitysympäristö

### Vaatimukset

- Node.js 18+
- Docker (PostgreSQL)

### 1. Tietokanta

```bash
docker compose up -d
```

### 2. Backend

```bash
cd backend
cp .env.example .env
# Muokkaa .env: DATABASE_URL, JWT_SECRET, MOBILEPAY_PHONE, CORS_ORIGIN
npm install
node src/db/migrate.js
npm run dev
```

API oletuksena: `http://localhost:3001`  
Health: `GET /api/health` → `{"ok":true}`

### 3. Frontend

```bash
cd frontend
npm install
npm run dev
```

Sovellus: `http://localhost:5173` — Vite proxyttaa `/api` ja `/logo.jpg` backendiin.

### Ensimmäinen admin / seed (valinnainen)

```bash
cd backend
node src/db/seed.js
```

Katso `seed.js` luotujen käyttäjien tiedot ennen ajoa.

## Ympäristömuuttujat (backend)

| Muuttuja | Kuvaus |
|----------|--------|
| `PORT` | API-portti (oletus 3001) |
| `DATABASE_URL` | PostgreSQL-yhteysmerkkijono |
| `JWT_SECRET` | Tokenien allekirjoitus |
| `CORS_ORIGIN` | Frontendin origin kehityksessä |
| `MOBILEPAY_PHONE` | MobilePay-numero maksulinkkejä varten |

## API (lyhyesti)

Kaikki reitit (paitsi kirjautuminen) vaativat `Authorization: Bearer <token>`.

| Prefiksi | Sisältö |
|----------|---------|
| `/api/auth` | Kirjautuminen, roster, salasanan vaihto, admin-käyttäjät |
| `/api/events` | Tapahtumat, ilmoittautumiset, toistuvat sarjat |
| `/api/fines` | Sakot |
| `/api/points` | Pistepörssi |
| `/api/invoices` | Laskut, maksut, joukkueen talous |
| `/api/injuries` | Loukkaantumiset |
| `/api/player-cards` | Pelaajakortit kaudelle |

## Tuotanto

Sovellus on tarkoitettu joukkueen omaan palvelimeen. Yksityiskohtaiset ohjeet:

- **Git + palvelin:** [PAIVITYS.md](./PAIVITYS.md)
- **WinSCP + `npm run build` omalla koneella:** [PAIVITYS-WINSCP.md](./PAIVITYS-WINSCP.md)

Tyypillinen setup: nginx tarjoilee `frontend/dist`, Node/PM2 (`jalpa-api`) pyörittää backendiä, Postgres Dockerissa.

## Lisenssi ja käyttö

Private / joukkueen sisäinen projekti. Älä jaa `.env`-tiedostoja tai tuotantotunnuksia.

---

Aiemman mobiiliprototyypin historia: [GitHub — JalPa](https://github.com/JoonasEskelinen/JalPa) (vanha Android-versio; nykyinen koodi on tässä repossa web-pinona).
