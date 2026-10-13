# Words in Context

A standalone, browser-based teaching tool showing how target–context frequencies can produce learned word vectors.

## Use

Open `index.html` in a modern browser, or serve this directory with any static website host. No build, dependencies, backend, accounts, or API keys are required. The single HTML file includes its own styles and scripts and works offline.

- Edit target–context counts to retrain.
- Add target rows and context columns.
- Suggested English/Czech emoji are editable. Click an icon in the table to open its picker. Icons do not affect training.
- Scrub 0–120 training passes; select points, labels, table rows, or cosine neighbours.
- Hover or focus controls for tooltips; the question-mark buttons also show help on tap.
- Save an experiment as JSON; load it elsewhere to reproduce its evidence and training.
- Browser storage saves the latest experiment when available. It is local to the origin and device, not shared with other visitors.

## GitHub Pages

1. Create a public repository (suggested name: `word-vectors`) and upload `index.html`, `.nojekyll`, and this README to its `main` branch.
2. Under **Settings → Pages**, choose **Deploy from a branch**, then `main` and `/ (root)`.
3. Save and wait for the Pages deployment to finish. GitHub will show the public URL.

For `otichy/word-vectors`, the expected URL is `https://otichy.github.io/word-vectors/`. This is an expected address, not a claim that it has already been published.

## Model

Simplified skip-gram with negative sampling: 2D target and context vectors; 120 shuffled passes; learning rate 0.035; three negatives per positive pair; negative distribution proportional to context frequency^0.75. Negatives are sampled with replacement and may include the observed context. There is no subsampling or text-window extraction. A deterministic seeded generator supports reproducibility; word initial vectors are keyed by spelling and role.

The chart shows actual target coordinates with equal coordinate-unit scale on both axes, not a dimensionality-reduction projection. The frame is fixed across the current training history. Separate context vectors are trained but are not plotted. Zero-count target rows remain at initial vectors. Emoji affect labels only.

Limits: 30 target rows, 24 context columns, integer counts from 0 to 100, word labels up to 32 characters. Training yields between batches so edits can cancel stale training. JSON imports are validated before replacing an experiment.

## Privacy

The application sends no words, counts, or saved experiments to a server. All computational work and emoji suggestions happen locally. Static hosting still receives ordinary page requests. External research links open only when clicked.

## Validation

JavaScript syntax and logic checks cover direct add handlers, English/Czech emoji suggestions, Unicode save/load round trips, import validation, deterministic training after reload, additional target/context training, and zero-count data. Browser-level visual and interaction QA was unavailable in the build environment and remains to be completed before declaring cross-browser compatibility.

## References

- Mikolov et al. (2013), *Distributed Representations of Words and Phrases and their Compositionality*. https://arxiv.org/abs/1310.4546
- Levy & Goldberg (2014), *Neural Word Embedding as Implicit Matrix Factorization*. https://proceedings.neurips.cc/paper_files/paper/2014/hash/b78666971ceae55a8e87efb7cbfd9ad4-Abstract.html
