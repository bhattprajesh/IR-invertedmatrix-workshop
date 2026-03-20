# 🔍 IR Inverted Matrix Workshop
### PROG8245-26W-Sec1-Machine Learning Programming | Conestoga College
**Individual Workshop Submission — 120 Minutes**

---

## 👤 Student Information

| Field | Details |
|-------|---------|
| **Name** | *Prajesh Jayesh Bhatt* |
| **Student ID** | *9046277* |
| **Team** | Team #4|
| **Date** | *20/03/2026* |
| **Course** | PROG8245 – Machine Learning Programming |

---

## 📂 Repository Structure

```
IR-invertedmatrix-workshop/
│
├── IR_InvertedMatrix_Workshop.ipynb   ← Main notebook (all challenges completed)
├── Student_Instructions_Workshop.ipynb
├── README.md                          ← This file
├── requirements.txt                   ← Minimal install dependencies
├── submit_assignment.py               ← Auto-submission script
├── .gitignore                         ← Git ignore rules
│
├── config/
│   └── required_items.json            ← Grader configuration
│
├── sample_docs/                       ← Corpus: 20 PSF blog posts
│   ├── blog_1.txt
│   ├── blog_2.txt
│   └── ...  (blog_3.txt → blog_20.txt)
│
└── utils/
    ├── validate_notebook.py           ← Validation + scoring logic
    └── submissions_log.csv            ← Auto-generated grade log
```

---

## 📚 Dataset

| Field | Details |
|-------|---------|
| **Source** | Python Software Foundation Official Blog |
| **URL** | https://pyfound.blogspot.com |
| **RSS Feed** | `https://pyfound.blogspot.com/feeds/posts/default?alt=rss` |
| **License** | [Creative Commons Attribution 4.0 (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/) |
| **Documents** | 20 blog posts |
| **Total tokens** | ~87,000 |
| **Unique vocabulary (raw)** | 4,800+ ✅ (threshold: > 2,000) |
| **Unique vocabulary (normalised)** | 3,900+ |

### Topics Covered in the Corpus
The 20 PSF blog posts span a variety of topics, giving the corpus a rich and diverse vocabulary:
- Python release announcements (3.11, 3.12, 3.13)
- PSF Board elections and governance
- Community grants and diversity initiatives
- Security advisories and CVE disclosures
- PyCon US and global conference news
- Open-source sustainability discussions

---

## 🛠️ Pipeline Architecture

```
Raw Text  (20 PSF blog posts)
    │
    ▼
┌─────────────────────────────────────────────────────────┐
│  Step 1 – Document Collection                           │
│  fetch_and_save_blog_posts() → load_documents()         │
└─────────────────────┬───────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│  Step 2 – Tokenization                                  │
│  tokenize()  →  regex \b\w+\b, lowercase                │
└─────────────────────┬───────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│  Step 3 – Normalization                                 │
│  normalize_tokens()  →  stop-word removal + stemming    │
└─────────────────────┬───────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│  Step 4 – Boolean Inverted Index                        │
│  build_inverted_index()  →  {term: [doc_id, ...]}       │
└───────┬─────────────────────────────────────────────────┘
        │
        ├──▶  Phrase Queries  (positional sequence matching)
        │
        ├──▶  Challenge 1: Standard Positional Index
        │     build_positional_index()
        │     {term: {doc_id: [pos_0, pos_1, ...]}}
        │
        ├──▶  Challenge 2: Memory-Efficient Positional Index
        │     build_positional_index_efficient()
        │     Uses array.array('i') → ~7× less RAM vs Python list
        │
        ├──▶  Challenge 3: IDF Scoring
        │     compute_idf_from_positional_index()
        │     IDF(t) = log(N / n_t)
        │
        └──▶  Challenge 4: TF + TF-IDF Scoring
              compute_tf()
              TF-IDF(t, d) = TF(t, d) × IDF(t)
```

---

## ✅ Completed Tasks

### Core Steps

| Task | Function | Status |
|------|----------|--------|
| Document Collection (20 docs, 2000+ vocab) | `fetch_and_save_blog_posts()` | ✅ |
| Load corpus into memory | `load_documents()` | ✅ |
| Tokenizer | `tokenize()` | ✅ |
| Normalization (stop words + stemming) | `normalize_tokens()` | ✅ |
| Boolean Inverted Index | `build_inverted_index()` | ✅ |
| 2 Phrase Queries tested | `phrase_query()` | ✅ |
| Query Processor (Boolean OR) | `query_processor()` | ✅ |

### Challenges

| Challenge | Task | Status |
|-----------|------|--------|
| **Challenge 1** | Standard Positional Index | ✅ |
| **Challenge 2** | Memory-Efficient Positional Index (`array.array`) | ✅ |
| **Challenge 3** | Inverse Document Frequency (IDF) | ✅ |
| **Challenge 4** | Term Frequency (TF) + TF-IDF scoring | ✅ |

### Documentation & Quality

| Item | Status |
|------|--------|
| Introduction section | ✅ |
| Reflection section with bigram preference discussion | ✅ |
| TF / IDF comparison table filled | ✅ |
| TF-IDF worked example with actual values | ✅ |
| All functions have NumPy-style docstrings | ✅ |
| All functions have edge-case guards | ✅ |
| Pandas DataFrames for all output tables | ✅ |
| Team talking points (one per step + code demo) | ✅ |
| Improvement discussion (BM25, lemmatisation, compression) | ✅ |

---

## 🧠 Key Concepts

### Inverted Index
Maps each normalised (stemmed) term to the sorted list of document IDs containing it. Enables O(1) term lookup instead of scanning every document on every query.

```python
inverted_index['python'] → [0, 1, 2, 3, ..., 19]
inverted_index['bylaw']  → [0, 14]
```

### Positional Index
Extends the inverted index by storing the exact token positions within each document — necessary for phrase and proximity queries.

```python
positional_index['python'][0] → [1, 15, 181]   # positions in doc 0
positional_index['python'][1] → [0, 12, 204]   # positions in doc 1
```

### Challenge 2 — Memory-Efficient Index
Standard Python lists store each integer as a 28-byte object. By switching to `array.array('i', ...)` (C `int32`, 4 bytes each), position lists consume **~7× less memory** with no change to the index structure or query logic.

| Implementation | Per-position cost | 87k-token corpus |
|---------------|-------------------|-----------------|
| `list` of Python ints | ~28 bytes | ~2.4 MB |
| `array.array('i')` | 4 bytes | ~340 KB |

### TF-IDF Worked Example
For the term **'software'** (stemmed: `'softwar'`) in Document 0:

```
TF('softwar', d0)  =  3          ← appears 3 times
IDF('softwar')     =  log(20/15) ≈ 0.2877
TF-IDF             =  3 × 0.2877 = 0.8631
```

`'softwar'` is common across the PSF corpus (15/20 docs → low IDF). A term
appearing in only 2 documents would score IDF ≈ 2.30 — a much stronger signal.

### Bigram Search Preference
For searching **bigrams** (consecutive word pairs), the **positional index** is far superior to a Term Document Count Matrix:

- **TDCM** — can only confirm both words exist in a document, not that they are adjacent.
- **Positional index** — checks `pos(w2) = pos(w1) + 1` in O(P) time, where P is the size of the smallest posting list. No raw-text rescan needed.

---


### Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| `jupyter` | ≥ 1.0.0 | Run `.ipynb` notebooks |
| `notebook` | ≥ 7.0.0 | Jupyter notebook interface |
| `feedparser` | ≥ 6.0.11 | Parse RSS feed for document collection |
| `requests` | ≥ 2.31.0 | HTTP requests |
| `nltk` | ≥ 3.8.1 | Stop-word lists + Porter Stemmer |
| `pandas` | ≥ 2.0.0 | Output DataFrames and tables |
| `nbformat` | ≥ 5.9.0 | Notebook validation (used by grader) |
| `gitpython` | ≥ 3.1.40 | Auto-push submission branch |

> The `array` module used in Challenge 2 is part of the Python standard library — no installation required.

---

```

**Expected passing output:**
```
=== Validation Report ===
✅ All required checks passed.

Score: 15 / 15 points
✅ Submission pushed to branch: submissions/team1
```


---

## 📄 License

Sample documents sourced from the **Python Software Foundation Blog** under
[Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).

---
