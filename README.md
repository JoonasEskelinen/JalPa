# JalPa - Urheilujoukkueen hallintasovellus ⚽

JalPa on React Native (Expo) -sovellus, joka on suunniteltu urheilujoukkueen sisäiseen budjetin seurantaan, laskutuksiin, tapahtumien nimenhuutoihin, sakkokassan hallintaan, sekä tilastojen seurantaan. Pelaajat voivat kirjautua nimen ja pelinumeron mukaan.



## Ominaisuudet

- **Salasanaton kirjautuminen:** Käyttää anonyymiä autentikaatiota yhdistettynä kustomoituun pelaajaprofiilien palautusjärjestelmään (Account Reclaim).
- **Sakkokassa:** Admin-oikeuksilla varustetut käyttäjät voivat merkata sakkoja pelaajille.
- **Nimehuuto (Events):** Tapahtumien luonti ja osallistujien seuranta.
- **Pelaajahallinta:** Joukkueen jäsenten listaaminen, muokkaus ja tilastot (sisäinen pistepörssi).
- **Talousnäkymä:** Joukkueen yhteisen talouden ja maksujen seuranta.

## 🛠 Teknologiat

- **Frontend:** React Native (Expo), TypeScript
- **Backend:** [Supabase](https://supabase.com/) (PostgreSQL, Auth)
- **Navigointi:** Custom Context-based navigation (eriytetty lib-kansioon)
- **Tyylit:** StyleSheet API kustomoidulla teemalla

## 🏗 Arkkitehtuuri ja Haasteet

### Auth Flow & Race Condition ratkaisu
Yksi projektin haasteista oli hallita anonyymin session ja tietokantaprofiilin välistä suhdetta. Toteutin ratkaisun, jossa:
1. Käyttäjä kirjautuu anonyymisti.
2. SQL-funktio (`reclaim_player`) päivittää pelaajan ID:n vastaamaan uutta sessiota.
3. Sovellus käyttää callback-mallia (`onLoginSuccess`) varmistaakseen, että näkymä vaihtuu vasta, kun tietokanta on 100 % synkronoitu.
