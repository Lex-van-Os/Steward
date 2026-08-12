# Steward API
Steward API is the backbone of Steward, managing the personal catalogue, notes, and flavor data that the AI engine and web interface both rely on, and keeping that data enriched with publicly available information on liquor, cigars, and tobacco.

## What it is
Steward API is the central service of Steward. It owns the personal catalogue of bottles, cigars, and tobacco blends, along with each user's tasting notes, ratings, and the shared flavor ontology that ties categories together. It's also responsible for ingesting data from public APIs on liquor, cigars, and tobacco, so a user's personal catalogue is enriched with curated outside information rather than relying on notes alone. It's the single source of truth that the web interface reads from and writes to, and that the AI engine draws on to generate recommendations.

## How it works
1. **Catalogue and notes are stored** — bottles, cigars, tobacco blends, tasting notes, and ratings, all tied to a shared flavor ontology.
2. **Public data is ingested** from external APIs covering liquor, cigars, and tobacco, and used to enrich a user's catalogue with curated outside information.
3. **The web interface talks to the API** to read and write a user's collection and notes.
4. **The API supplies the AI engine** with catalogue, flavor, and enrichment data, and relays the resulting recommendations back to the web interface.
5. **It runs as one of several small, independent services** behind a reverse proxy, self-hosted on a single machine.