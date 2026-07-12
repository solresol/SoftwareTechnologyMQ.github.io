# IMPROVEMENTS.md

*Analysis date: 2026-07-11*

This repository is the Jekyll (kramdown + KaTeX, minima theme) source for the Macquarie University Software Technology teaching site (softwaretechnologymq.github.io): ~100 lesson Markdown files covering COMP1000/1010/2160/6010 topics (variables through recursion, sorting, Python, Processing, Java), plus committed Java sample code, figure directories, and a pandoc-based slide-generation pipeline (`codex-setup.sh`, `.github/workflows/sorting-slides.yml`). Recent work (PRs #6–#12) has focused entirely on `sorting.md` and its slide workflow. The tree is currently clean; the state below reflects accumulated housekeeping debt rather than broken code.

## Bugs & Fixes

- **Three competing index files**: `index.md`, `index.markdown`, and `_index.markdown` all exist at the root. `index.md` and `index.markdown` will collide (both map to `/index.html`); Jekyll's behaviour depends on processing order. Keep one (probably `index.md`, which appears to be the real landing page), delete the other two.
- **Editor junk committed**: `Gemfile-e` and `Gemfile-e-e` are `sed -i -e` backup artefacts and differ from the real `Gemfile`. Delete both and add `*-e` to `.gitignore`.
- **Empty `description:` in `_config.yml`** — the `>-` block has no content, and `url:`/`baseurl:` are empty strings. Set `url: "https://softwaretechnologymq.github.io"` so `jekyll-seo-tag`/feed URLs are absolute.
- **Root clutter breaks the site build model**: loose `MyArrayList.java`, `Node.java`, `Rectangle.java`, `sorting.pdf`, `block_ans.png`, `memoryDiagramBasic.png` at repo root get copied verbatim into the published site. Move Java sources under `codes/` and images under `assets/` (or `fig/`), then fix the referencing pages (`custom-built-arraylist.md`, `Rectangle.md`, `RectangleClient.md`).

## Improvements

- **Generalize the slide workflow**: README tells contributors to duplicate `sorting-slides.yml` per lesson. Instead, use a single matrix (or `on: workflow_dispatch` with an input) workflow that runs pandoc over any changed `*.md`, or over a declared list in `_data/`. One file, zero duplication.
- **Navigation/index generation**: with ~100 topic pages, a hand-edited index is fragile. Use front-matter (`course:`, `topic-order:`) plus a Liquid loop in `index.md` to auto-build the course listings; `_data/` is already present and could hold the course→page map.
- **`search.html` exists** — verify it actually indexes all pages; a lunr.js or Pagefind build step in CI would make the site far more usable for students.
- **Consistent naming**: file names mix `snake_case`, `camelCase` (`basicListFunctions.md`, `listOfLists.md`), and `kebab-case` (`recursion-arrays.md`). Renames break student bookmarks, so if standardizing, add `redirect_from` (jekyll-redirect-from plugin).

## Testing / CI

- **No build check on PRs**: the contribution flow in README invites web-edit PRs from students, but there is no CI that runs `bundle exec jekyll build` to catch broken front-matter/Liquid. Add a simple build-and-htmlproofer workflow (link checking would catch dead cross-references between the many lesson pages).
- **Compile the Java samples**: a CI step that runs `javac` over `codes/` and the root `.java` files would keep teaching code honest.
- `include_usage.rb` at root looks like a one-off audit script — either wire it into CI or move it to a `scripts/` directory with a comment on how to run it.

## Documentation

- README covers the PR flow and slide generation but not local preview. Add: `bundle install && bundle exec jekyll serve` (and note Ruby version; `Gemfile.lock` should be checked for a modern Jekyll — currently pinned `~> 4.2.2`, worth bumping to 4.3.x).
- Document the slide-level convention (`--slide-level=4`) inside each lesson file's front-matter or a CONTRIBUTING.md so new lesson authors write headings that slide-split correctly.
- `sweep.yaml` references the (defunct) Sweep AI bot — delete it or replace with current tooling notes.

## Security

- No secrets found. `codex-setup.sh` uses `sudo apt-get` — fine for CI, but note in README it is for Linux CI runners only, not contributor machines.

## Housekeeping / Modernization

- Delete or archive `testing_ground.md`, `sampleSVG.md`, and `Gemfile-e*` (see Bugs).
- `404.html`, `about.markdown` are stock minima leftovers — customize or remove `about.markdown`.
- Consider moving all lesson pages into a `_lessons/` Jekyll collection: gives clean permalinks, per-collection layouts, and makes the auto-index above trivial. This is the single highest-leverage structural change.
- Commented-out cruft in `_config.yml` (twitter/github usernames, email) can be pruned.

## Quick Wins

1. `git rm Gemfile-e Gemfile-e-e` (2 minutes, removes confusion).
2. Delete two of the three index files.
3. Set `url:` in `_config.yml`.
4. Add a `jekyll build` PR check.
5. Replace per-lesson slide workflows with one matrix workflow.

*(No previous IMPROVEMENTS.md existed; this is the first version.)*
