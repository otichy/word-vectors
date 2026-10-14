# Words in Context

A standalone, browser-based teaching tool showing how target–context frequencies can produce learned word vectors.

## Use

Open `index.html` in a modern browser, or serve this directory with any static website host. No build, dependencies, backend, accounts, or API keys are required. The single HTML file includes its own styles and scripts and works offline.

- Edit target–context counts to retrain, or paste a corpus and click **Recount from text**.
- Choose a symmetric context window (1–10 tokens each side). Only existing table labels contribute counts; other words still occupy positions.
- See the estimated language and whether English normalization is active. Override uncertain detection when needed. The preview shows token transformations.
- Add target rows and context columns.
- Suggested English/Czech emoji are editable. Click an icon in the table to open its picker. Icons do not affect training.
- Scrub 0–120 training rounds; select points, labels, table rows, or cosine neighbours.
- Hover or focus controls for tooltips; the question-mark buttons also show help on tap.
- Save an experiment as JSON; load it elsewhere to reproduce its evidence and training.
- Browser storage saves the latest experiment when available. It is local to the origin and device, not shared with other visitors.

## GitHub Pages

1. Create a public repository (suggested name: `word-vectors`) and upload `index.html`, `.nojekyll`, and this README to its `main` branch.
2. Under **Settings → Pages**, choose **Deploy from a branch**, then `main` and `/ (root)`.
3. Save and wait for the Pages deployment to finish. GitHub will show the public URL.

Public tool: https://otichy.github.io/word-vectors/

## Model

Simplified skip-gram with negative sampling: 2D target and context vectors; 120 rounds; learning rate 0.035; three negatives per positive pair; negative distribution proportional to context frequency^0.75. Negatives are sampled with replacement and may include the observed context. Tables up to 10,000 pairs use shuffled full passes. Larger tables use 10,000 frequency-weighted positive samples per round, with replacement; counts are never expanded into an unbounded training array. A deterministic seeded generator supports reproducibility; word initial vectors are keyed by spelling and role.

The chart shows actual target coordinates with equal coordinate-unit scale on both axes, not a dimensionality-reduction projection. The frame is fixed across the current training history. Separate context vectors are trained but are not plotted. Zero-count target rows remain at initial vectors. Emoji affect labels only.

Limits: 30 target rows, 24 context columns, integer counts from 0 to 1,000,000,000, word labels up to 32 characters. Training yields between batches so edits can cancel stale training. JSON imports are validated before replacing an experiment.

## Corpus preprocessing

Unicode letter tokens are lowercased and accents preserved. Internal apostrophes stay in tokens; hyphens separate words. Periods, question marks, exclamation marks, and line breaks bound counting windows (including periods in abbreviations). No stopwords are removed. A position cannot be its own context, but repeated occurrences of the same word can pair.

Only English receives morphological normalization: original Porter stemming plus a small common-irregular-form dictionary. This is not contextual lemmatization; stems can be fragments and can conflate words. Both corpus and table labels receive the same processing. Duplicate normalized labels within a vocabulary are rejected when recounting; use one label per normalized form. Multiword labels cannot be recounted.

Language identification is a small local heuristic using common-word evidence from the first 60,000 characters, not a trained detector. It considers English, Czech, Slovak, German, French, Spanish, Italian, Portuguese, Dutch, Polish, Russian, and Ukrainian. Short or ambiguous evidence returns uncertain and disables English normalization. Detection can be wrong, particularly for unsupported or mixed-language texts; use the manual override. Unicode letter matching does not perform dictionary-based word segmentation for languages without spaces.

Corpus limit: 1,000,000 characters. Recounting replaces every table cell and retrains; editing text alone leaves counts unchanged and marks them as needing recount. Corpus text and settings are included in saved JSON experiments. Existing version-1 files without corpus fields remain supported.

## Privacy

The application sends no words, counts, or saved experiments to a server. All computational work and emoji suggestions happen locally. Static hosting still receives ordinary page requests. External research links open only when clicked.

## Validation

JavaScript syntax and logic checks cover direct add handlers, English/Czech emoji suggestions, Unicode save/load round trips, import validation, deterministic training after reload, additional target/context training, and zero-count data. Additional checks cover hand-counted corpus examples, sentence/line boundaries, window distances, self-position exclusion, English irregulars, non-English preservation, detection uncertainty, normalized-label collisions, legacy imports, and bounded training with billion-count cells. Browser testing does not imply universal cross-browser compatibility.

## References

- Mikolov et al. (2013), *Distributed Representations of Words and Phrases and their Compositionality*. https://arxiv.org/abs/1310.4546
- Levy & Goldberg (2014), *Neural Word Embedding as Implicit Matrix Factorization*. https://proceedings.neurips.cc/paper_files/paper/2014/hash/b78666971ceae55a8e87efb7cbfd9ad4-Abstract.html

- Porter stemming: https://snowballstem.org/algorithms/porter/stemmer.html
