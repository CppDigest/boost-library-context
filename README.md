# boost-library-context

Context data from **Boost** libraries on GitHub: **issues** and **pull requests** from the [boostorg](https://github.com/boostorg) organization.

## What's in this repo

- **Issues** — Bug reports, questions, and discussions from GitHub issues (e.g. `github.com/boostorg/<library>/issues`).
- **Pull requests** — Code review discussions and merged/closed PRs from GitHub (e.g. `github.com/boostorg/<library>/pull`).

Each issue and PR is stored as one Markdown file, grouped by library, year, and month. This gives you a local, searchable snapshot of Boost’s GitHub issue and PR history for use with search, RAG, or other tooling.

## Why use this repo

- **Offline and local** — Work with Boost issue/PR content without hitting the GitHub API or scraping the site.
- **Stable paths** — Consistent layout (`boost.<library>/issues/YYYY/YYYY-MM/`, `.../pull_requests/...`) makes it easy to index, embed, or link from other tools.
- **Single place** — One tree for many Boost libraries (boost, boost.accumulators, boost.context, etc.) so you can search or analyze across the whole set.

Use this repo when you want historical and current context from Boost’s GitHub issues and pull requests in one place.

## Repository structure

### Folder layout

```
boost-library-context/
├── .gitignore
├── README.md           # This file
├── boost/              # Main Boost superproject (boostorg/boost)
│   ├── issues/
│   │   ├── 2018/
│   │   │   ├── 2018-08/   # Files: "#123 - Title.md", "#124 - Title.md", ...
│   │   │   ├── 2018-09/
│   │   │   └── ...
│   │   ├── 2019/
│   │   ├── ...
│   │   └── (year/month nesting)
│   └── pull_requests/
│       ├── 2019/
│       │   ├── 2019-01/
│       │   └── ...
│       └── (year/month nesting)
├── boost.accumulators/ # boostorg/accumulators
│   ├── issues/
│   │   └── YYYY/
│   │       └── YYYY-MM/
│   └── pull_requests/
│       └── YYYY/
│           └── YYYY-MM/
├── boost.algorithm/
├── boost.context/
├── ...                 # One top-level folder per library (~172 libraries)
└── (same issues/ and pull_requests/ layout under each)
```

- **`boost/`** — Issues and PRs for the main Boost repo ([boostorg/boost](https://github.com/boostorg/boost)).
- **`boost.<library>/`** — Issues and PRs for each Boost library repo (e.g. `boost.accumulators` → [boostorg/accumulators](https://github.com/boostorg/accumulators)). One folder per repo, with **year** then **YYYY-MM** under both `issues/` and `pull_requests/`.
- **issues/** — One `.md` file per GitHub issue. Filenames: `#<id> - <short title>.md`.
- **pull_requests/** — One `.md` file per GitHub pull request. Filenames: `#<id> - <short title>.md`.

### Issue file structure (`.md`)

Each issue file contains:

| Section             | Description                                                              |
| ------------------- | ------------------------------------------------------------------------ |
| **Title line**      | `# #<id> - <title> [<status>]` (e.g. `[Closed]`)                         |
| **Metadata**        | `Username`, `Created at`, `Updated at`, `Closed at`, `Url` (GitHub link) |
| **Body**            | Issue description (Markdown)                                             |
| **Comment 1, 2, …** | Each comment: `## Comment N`, then `Username`, `Created at`, `Url`, body |

Example header:

```markdown
# #103 - Bug in test Jamfile [Closed]

> Username: Flamefire
> Created at: 2020-02-20 19:35:11 UTC
> Updated at: 2020-04-09 15:29:19 UTC
> Closed at: 2020-04-09 08:53:52 UTC
> Url: https://github.com/boostorg/yap/issues/103

It seems https://github.com/boostorg/yap/blob/... is wrong.
...

---

## Comment 1

> Username: tzlaine
> Created at: 2020-04-08 23:31:23 UTC
> Url: https://github.com/boostorg/yap/issues/103#issuecomment-611246188
> ...
```

### Pull request file structure (`.md`)

Each PR file contains the main description plus **comments** and **reviews**.

- **Comments** (`## Comment 1`, `## Comment 2`, …) — General PR discussion. Same style as issue comments: `Username`, `Created_at`, `Url` (pointing to `#issuecomment-...`), then body. No file or code attachment.
- **Reviews** (`## Review 1 [Commented]`, …) — Code-review feedback. Each review has:
  - **Metadata**: `Username`, `Created_at`, `State` (e.g. `COMMENTED`), `Url` (pointing to `#pullrequestreview-...`).
  - **Code part**: a file path (often prefixed with 📁) and a **code block** (e.g. `diff` with line numbers like ` 111 |` or ` 115 |-`) showing the exact lines being reviewed.
  - **Discussion thread**: one or more replies, each with `Username`, `Created_at`, `Updated_at`, `Url` (pointing to `#discussion_r...`), and body (can include code blocks).

So: PRs contain **comments** (general discussion) and **reviews** (reviews attach to a place in the diff and include the **code part** plus a thread of review discussion). Not every PR has review blocks; many have only comments.

| Section              | Description                                                                                                                                             |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Title line**       | `# #<id> <title> [<status>]` (e.g. `[Merged]`, `[Closed]`)                                                                                              |
| **Metadata**         | `Username`, `Created at`, `Updated at`, `Merged at`, `Closed at`, `Url`                                                                                 |
| **Body**             | PR description (Markdown)                                                                                                                               |
| **Comment N**        | General comment: `Username`, `Created_at`, `Url` (#issuecomment-…), body                                                                                |
| **Review N [State]** | Code review: metadata, then **file path** (e.g. 📁 path), **code block** (diff/snippet with line numbers), then **discussion replies** (#discussion_r…) |

Example — PR with one comment and one review (review includes code part and discussion):

```markdown
# #88 VS2017 support [Merged]

> Username: Kojoley
> Created at: 2018-10-08 15:44:33 UTC
> Updated at: 2018-10-20 19:26:15 UTC
> Merged at: 2018-10-20 19:26:15 UTC
> Url: https://github.com/boostorg/yap/pull/88

With these fixes/workarounds the library is usable on msvc-14.1 and all tests pass.

---

## Review 1 [Commented]

> Username: tzlaine
> Created_at: 2018-10-18 06:58:44 UTC
> State: COMMENTED
> Url: https://github.com/boostorg/yap/pull/88#pullrequestreview-165947226

📁 test/comma.cpp

diff
111 | term<void_callable>{{&call_count, &void_called}}());
112 |
115 |- BOOST_MPL_ASSERT(

> Username: tzlaine
> Created_at: 2018-10-18 06:58:44 UTC
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226187173

Most of these changes look good, but why are you having trouble with BOOST_MPL_ASSERT? ...

> Username: Kojoley
> Created_at: 2018-10-18 15:52:56 UTC
> Url: https://github.com/boostorg/yap/pull/88#discussion_r226363622

comma.cpp
libs\yap\test\comma.cpp(116): error C2955: ...

## IIUC MSVC have some problems with `decltype` in macros.

## Comment 2

> Username: tzlaine
> Created_at: 2018-10-19 01:32:03 UTC
> Url: https://github.com/boostorg/yap/pull/88#issuecomment-431216294

## Any idea why the Appveyor build still fails?
```

---

_This repo holds a snapshot of Boost library issues and pull requests from GitHub (boostorg) for use as context in search, RAG, or analysis._
