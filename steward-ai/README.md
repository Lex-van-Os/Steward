# Steward AI
Steward AI is the recommendation engine behind Steward, turning flavor notes and a shared flavor ontology into pairing suggestions that are grounded in real data rather than guessed.

## What it is
Steward AI is the service responsible for reasoning about flavor. It takes the shared flavor ontology and a user's catalogue, notes, and ratings, and turns them into recommendations that explain themselves — pairings across wine, whisky, cigars, and tobacco that are traceable back to actual data, not invented by a chatbot. It's built as an independent service so the reasoning layer can evolve separately from the API and web interface it serves.

## How it works
1. **Flavor data comes in** from the API — catalogue items, tasting notes, and the shared flavor ontology that ties every category together.
2. **Embeddings represent flavor profiles as vectors**, so items can be compared for similarity across categories, not just matched on keywords.
3. **A retrieval-augmented approach grounds recommendations** in the user's own notes and curated public data, so every suggestion comes with a traceable explanation.
4. **Recommendations are served back to the API**, which surfaces them to the web interface.