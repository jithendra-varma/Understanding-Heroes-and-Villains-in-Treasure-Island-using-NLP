# Understanding Heros and Villains in *Treasure Island* (NLP Pipeline)

This project builds an end-to-end NLP pipeline to analyze **character sentiment** in Robert Louis Stevenson’s *Treasure Island* and infer narrative roles such as **Hero**, **Villain**, **Morally Complex**, and **Support/Neutral** using:

- Character-level sentiment polarity (VADER)
- Sentiment volatility across chapters (standard deviation)
- Coreference resolution (Coreferee + spaCy Transformer)
- NER + alias mapping to unify character mentions
- Quote attribution + dependency-based sentiment targeting for better sentiment attribution

---

## What this project does

### 1) Load & clean the text
- Removes Project Gutenberg boilerplate and keeps only the story.
- Saves cleaned text as `cleaned_treasure_island.txt`.

### 2) Segment chapters + tokenize sentences
- Splits chapters using regex (Roman numeral chapter markers).
- Tokenizes sentences using spaCy and creates a structured dataframe.

### 3) Resolve coreferences
- Uses `en_core_web_trf` + `coreferee` to reduce pronouns and improve mention clarity.
- Includes a simple check comparing pronoun presence before vs after coref.

### 4) Extract character mentions (NER)
- Extracts `PERSON` entities and counts unique mentions.
- Builds a mention-frequency view to understand major vs minor characters.

### 5) Alias mapping + manual role mapping
- Maps variations like `silver`, `long john`, `john silver` → **Long John Silver**
- Adds role-based mappings NER often misses:
  - `doctor` → Dr. David Livesey
  - `squire` → Squire Trelawney
  - `blind man` → Pew
  - `gamekeeper` → Tom Redruth

### 6) Sentiment attribution (VADER)
Sentiment is assigned using a layered approach:

1. **Quote sentiment** (speaker-attributed where possible)  
2. **Dependency sentiment targets** (e.g., “Jim is brave”, “brave Jim”)  
3. **Fallback**: if no direct character sentiment is found, sentence sentiment is assigned to mentioned characters.

### 7) Character analytics + visualisations
- Major vs minor characters based on mention frequency
- Per-character summary statistics:
  - Total mentions, average/median sentiment, quartiles, min/max
- Sentiment arcs across chapters (character trajectories)
- Volatility analysis (average chapter-wise std. dev. per character)

### 8) Role inference: Hero vs Villain vs Morally Complex
- X-axis: **Average sentiment**
- Y-axis: **Volatility**
- Volatility threshold (`V*`) is chosen as the **KDE peak**
- A small “support/neutral” square is placed near neutral sentiment and around the KDE peak volatility

---

## Outputs

- `character_sentiment_summary.csv`
  - TotalMentions, AvgSentiment, MedianSentiment, quartiles, min/max per character
- Role table (`role_df`)
  - AvgSentiment, SentimentStdDev, TotalMentions, RefinedRole
- Visualisations:
  - Sentiment arcs for top major/minor characters
  - Volatility distribution + KDE peak
  - Role map (Sentiment vs Volatility)

---

## Tech stack

- Python
- pandas, numpy
- spaCy (`en_core_web_trf`)
- coreferee (coreference resolution)
- NLTK + VADER Sentiment
- matplotlib, seaborn

---

## How to run

### 1) Create a virtual environment
```bash
python -m venv .venv
```

### 2) Activate it

**macOS/Linux**
```bash
source .venv/bin/activate
```

**Windows**
```bash
.venv\Scripts\activate
```

### 3) Install dependencies
```bash
pip install -r requirements.txt
```

### 4) Install spaCy model
```bash
python -m spacy download en_core_web_trf
```

### 5) Install coreferee language data (if needed)
```bash
python -m coreferee install en
```

### 6) Run the notebook
```bash
jupyter notebook
```

---

## Notes / limitations

- Coreference resolution can sometimes replace mentions incorrectly depending on context.
- Titles like **“captain”** can be ambiguous (Smollett vs Bones vs Flint vs Silver).
- VADER is lexicon-based and may miss sarcasm or deeper context.

---

## Future improvements

- Upgrade sentiment scoring using transformer sentiment models.
- Improve quote attribution with stronger parsing and multi-speaker tracking.
- Better disambiguation for titles (“captain”) and shared names (“Tom”).

---

## License

This repository is for academic/research use.  
Please follow Project Gutenberg terms for the original text.
