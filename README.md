# Words in Context

A standalone, browser-based teaching tool showing how target–context frequencies can produce learned word vectors.

## Use

Open `index.html` in a modern browser, or serve this directory with any static website host. No build, dependencies, backend, accounts, or API keys are required. The single HTML file includes its own styles and scripts and works offline.

- Start immediately with the predefined table. All three steps collapse independently; corpus assembly is optional.
- Edit target–context counts to retrain, or paste a corpus and click **Recount from text**.
- Choose a symmetric context window (1–10 tokens each side, default 5). Only existing table labels contribute counts; other words still occupy positions.
- See the estimated language and whether English normalization is active. Override uncertain detection when needed. The preview shows token transformations.
- Add target rows and context columns, or remove target rows and context columns with the small ? controls (keeping at least one of each). **Suggest contexts** offers up to 12 corpus-derived columns, with per-target counts, shared/distinctive labels, minimum-count filtering and optional function-word exclusion. Select candidates, then add and recount.
- Suggested English/Czech emoji are editable. Only target words have icons; click a target icon in the table to open its picker. Context columns use text labels. Icons do not affect training.
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

Corpus limit: 10,000,000 characters. Recounting and suggestions run in a Web Worker; suggestion vocabulary is capped at 50,000 candidates. Corpora over 1,000,000 characters use file saves instead of synchronous automatic browser storage. Recounting replaces every table cell and retrains; editing text alone leaves counts unchanged and marks them as needing recount. Corpus text and settings are included in saved JSON experiments. Existing version-1 files without corpus fields remain supported.

### Example corpus

New experiments include a 36-sentence English corpus that exactly reconstructs the predefined 4×4 matrix at window ±5: cat and dog each have [8, 8, 2, 0], car and bus each have [0, 0, 8, 8], for contexts sleeps/eats/moves/stops. Total: 68 pairs. Sentence boundaries keep examples separate. **Reset to defaults** restores the table, example text, and settings; click Recount to derive counts. Previously saved text and settings remain intact on restore.

### Context suggestions

Shared suggestion rows are green and distinctive rows purple, with text labels retained for accessibility and matching dark-mode colors. Candidates are counted in the same target windows as the table. Shared candidates rank by target coverage then frequency; distinctive candidates rank by maximum PPMI × log₂(1 + pair count). PPMI uses marginals over all target-window pairs before filtering, not corpus-wide token marginals. The mixture takes up to six shared and six additional distinctive contexts, filling remaining slots by frequency. Existing context columns and target words are excluded. Most-frequent surface forms label normalized groups. The minimum total pair count defaults to 2. Function-word filtering uses small local lists and does not remove tokens from window positions. Review counts before selecting; no candidates are automatically added. Stale suggestions are rejected if the text, vocabulary, or settings change.

## Privacy

The application sends no words, counts, or saved experiments to a server. All computational work and emoji suggestions happen locally. Static hosting still receives ordinary page requests. External research links open only when clicked.

## Validation

JavaScript syntax and logic checks cover direct add handlers, English/Czech emoji suggestions, Unicode save/load round trips, import validation, deterministic training after reload, additional target/context training, and zero-count data. Additional checks cover hand-counted corpus examples, sentence/line boundaries, window distances, self-position exclusion, English irregulars, non-English preservation, detection uncertainty, normalized-label collisions, legacy imports, and bounded training with billion-count cells. Browser testing does not imply universal cross-browser compatibility.

## References

- Mikolov et al. (2013), *Distributed Representations of Words and Phrases and their Compositionality*. https://arxiv.org/abs/1310.4546
- Levy & Goldberg (2014), *Neural Word Embedding as Implicit Matrix Factorization*. https://proceedings.neurips.cc/paper_files/paper/2014/hash/b78666971ceae55a8e87efb7cbfd9ad4-Abstract.html

- Porter stemming: https://snowballstem.org/algorithms/porter/stemmer.html
