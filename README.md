# PedalPal Help — FAQ Chatbot (TF-IDF + Cosine Similarity)

A self-contained FAQ chatbot that matches a user's question against a set of
predefined FAQs using a small NLP pipeline written from scratch in vanilla
JavaScript — no frameworks, no backend, no API keys. Open `index.html` and
it runs entirely in the browser, which makes it a clean drop-in for GitHub
Pages.

**[Live demo →](#)** *(replace with your GitHub Pages URL once deployed)*

![chatbot screenshot placeholder](#)

## How it works

The bot is themed around a fictional bike-share app ("PedalPal") so the FAQ
set has realistic variety (account, billing, safety, parking,
troubleshooting), but the pipeline is generic — swap the `FAQS` array for
your own product or topic and everything else keeps working.

Pipeline, step by step:

1. **Tokenize** — lowercase the text, strip punctuation, split on whitespace.
2. **Remove stopwords** — filter out common low-signal words (the, is, a,
   with, ...) using a built-in stopword list.
3. **Stem** — reduce words to a common root with a lightweight,
   Porter-inspired suffix-stripping stemmer (`running` → `run`,
   `cancellation` → `cancel`), so "How do I cancel my plan?" and
   "cancelling my membership" line up.
4. **Vectorize with TF-IDF** — build a vocabulary across all FAQs, then
   represent each FAQ and each user question as a TF-IDF vector (term
   frequency weighted by inverse document frequency, so common words across
   the FAQ set count for less).
5. **Match with cosine similarity** — compare the user's question vector
   against every FAQ vector and return the closest match.
6. **Confidence threshold** — if the best score is below a threshold
   (`0.12` by default), the bot admits it doesn't have a confident answer
   instead of guessing, and shows that as a visible "signal" meter under
   each response.

No external NLP library (NLTK, spaCy, scikit-learn) is used — the whole
pipeline (tokenizing, stopword removal, stemming, TF-IDF, cosine similarity)
is implemented directly in `index.html` in plain JavaScript so the project
has zero dependencies and zero build step.

## Project structure

```
.
├── index.html   # everything: markup, styles, FAQ data, NLP pipeline, chat UI
└── README.md
```

Everything lives in one file on purpose — it makes the project trivial to
fork, read top-to-bottom, and deploy.

## Running it locally

No install step needed.

```bash
git clone https://github.com/<your-username>/<your-repo>.git
cd <your-repo>
open index.html        # macOS
# or just double-click index.html / drag it into a browser
```

## Deploying to GitHub Pages

1. Push this repo to GitHub.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`,
   pick your default branch and the `/ (root)` folder.
4. Save — GitHub will give you a URL like
   `https://<your-username>.github.io/<your-repo>/` within a minute or two.

## Customizing it for your own FAQs

Open `index.html` and edit the `FAQS` array near the top of the `<script>`
block:

```js
const FAQS = [
  { cat: "Billing", q: "How do I update my card?", a: "Go to Settings → Payment → Edit." },
  // add as many as you want — more FAQs = a richer vocabulary = better matching
];
```

Everything else — the vocabulary, the TF-IDF weights, the sidebar topic
chips, the sample-question list — is generated automatically from this
array, so there's nothing else to wire up.

Other things worth tuning:

- `THRESHOLD` (in the JS) — raise it to make the bot more conservative about
  admitting it doesn't know, lower it to make it answer more often even when
  unsure.
- `STOPWORDS` — extend this if your domain has its own filler words.
- The color tokens at the top of the `<style>` block (`--pine`, `--moss`,
  `--signal`, etc.) — change these to restyle the whole app for your own
  brand without touching layout code.

## Why TF-IDF + cosine similarity instead of an LLM?

This project is meant to demonstrate the classical information-retrieval
approach to FAQ matching: build a vector space from the FAQ corpus, score a
query against every entry, and return the closest one. It's fast, fully
explainable (you can literally show the vocabulary and weights), needs no
API key or network call, and is a solid baseline before reaching for
embeddings or an LLM-based intent classifier.

## Possible extensions

- Swap the hand-rolled stemmer for a full Porter Stemmer or a proper
  lemmatizer if you move the pipeline to Python (NLTK/spaCy) with a small
  Flask/FastAPI backend.
- Replace TF-IDF with sentence embeddings (e.g. `sentence-transformers`) for
  better semantic matching on paraphrased questions.
- Log unmatched questions to find gaps in the FAQ set.
- Add multi-turn context so follow-up questions like "and how much is that?"
  resolve against the previous answer's topic.

## License

MIT — use this however you like.
