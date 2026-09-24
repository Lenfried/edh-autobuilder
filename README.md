# edh-autobuilder

## Components

### 1. Preprocessing & Embedding Pipeline
* **Scryfall Data Filtration:** Automatically processes bulk card data, filtering specifically for Commander-legal cards while removing non-playable elements (Tokens, Emblems, Schemes).
* **Text Vectorization:** Leverages the `all-mpnet-base-v2` transformer model to construct high-dimensional dense vectors representing the semantics of card oracle rules text.
* **Feature Engineering:** One-Hot Encodes categorical features (e.g., rarity), multi-label encodes color identities, and standardizes numerical fields (e.g., power, toughness, mana values) using a `MinMaxScaler`.

### 2. Genetic Algorithm Optimization (Part 1)
Our core deck recommendation system runs on an evolutionary heuristic algorithm designed to evolve a randomly generated legal card pool into an optimized non-land deck layout of **61 spells**:
* **Individual:** Represented as a list of 61 unique card indices mapped to the filtered card database.
* **Crossover:** A customized slice-and-merge crossover operator that prevents card duplication while maintaining structural integrity.
* **Mutation:** Randomly replaces active spell selections with clean matches from the legal candidate pool.
* **Multi-Factor Fitness Function:** Evaluates individual decks using dynamically weighted objectives:
  $$\text{Fitness} = w_1 \cdot \text{Alignment} + w_2 \cdot \text{Cohesion} + w_3 \cdot \text{Structure}$$
  * **Commander Alignment:** Cosine similarity of card embeddings to the selected Commander's rules text.
  * **Intra-Deck Cohesion:** Pairwise cosine similarity among all spells in the deck.
  * **Structure Satisfaction:** Monitors and optimizes target ratios for key deck functionalities (10 Ramp, 10 Card Draw, 10 Interaction spells), penalizing severe deviations.

### 3. Metric Evaluation Suite (Part 2)
To confirm the scientific rigor and thematic efficacy of our evolutionary output, we evaluate the generated decks using four core alignment metrics:
1. **Jaccard Similarity (Oracle Text & Name):** Computes text and keyword similarity overlaps.
2. **Pairwise Cosine Similarity (Mean Pooling):** Pools embeddings into a single deck vector to compare global thematic footprints.
3. **Symmetric Best-Match (SBM) Cosine Similarity:** Maps every card in Deck A to its closest thematic equivalent in Deck B, protecting multi-thematic structures.
4. **EDHRec Synergy Score:** Scores deck listings against verified community deck construction statistics on EDHRec.

---

## Validation & Benchmark Results

The validation pipeline evaluates model outputs across several critical bounds:
* **20% Perturbation Testing:** Tests how metrics respond when exactly 20% of a cohesive deck is swapped out for random cards.
* **Cross-Commander Alignment:** Evaluates thematic distance when mapping a deck built for a thematic commander against an entirely unrelated or mechanically distinct commander.
