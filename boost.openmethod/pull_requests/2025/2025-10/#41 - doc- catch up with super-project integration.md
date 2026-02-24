# #41 doc: catch up with super-project integration [Merged]

> Username: jll63  
> Created at: 2025-10-24 01:21:55 UTC  
> Updated at: 2025-10-25 15:40:11 UTC  
> Merged at: 2025-10-24 19:52:31 UTC  
> Closed at: 2025-10-24 19:52:31 UTC  
> Url: https://github.com/boostorg/openmethod/pull/41  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-10-24 01:22:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/41#pullrequestreview-3373825413  

## Pull Request Overview  
  
This PR updates documentation integration to align with the super-project structure. The main changes include updating redirect paths, removing redundant redirect files, and adjusting build script output paths.  
  
- Updated redirect URLs from Antora-based paths to simplified `doc/html/index.html`  
- Added copyright header to root `index.html`  
- Modified build script to reference the correct output directory  
  
### Reviewed Changes  
  
Copilot reviewed 3 out of 3 changed files in this pull request and generated 1 comment.  
  
| File | Description |  
| ---- | ----------- |  
| index.html | Updated redirect path and added copyright header |  
| doc/index.html | Removed redundant redirect file |  
| doc/build_antora.sh | Updated output directory path from `build/site` to `html` |  
  
  
  
  
  
---  
  
<sub>**Tip:** Customize your code reviews with copilot-instructions.md. <a href="/boostorg/openmethod/new/develop/.github?filename=copilot-instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Create the file</a> or <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">learn how to get started</a>.</sub>

📁 index.html

```diff
   1 |+ 
   2 |+ <!--
   3 |+ Copyright 2005-2007 Daniel James.
```

> Username: Copilot  
> Created_at: 2025-10-24 01:22:16 UTC  
> Updated_at: 2025-10-24 01:22:17 UTC  
> Url: https://github.com/boostorg/openmethod/pull/41#discussion_r2458269492  

The copyright notice appears to be incorrect for this project. The removed content shows 'Copyright (C) 2022 Jean-Louis Leroy' and mentions 'Boost.OpenMethod', but the added copyright references 'Daniel James' from 2005-2007. This should likely reflect the actual project copyright holder and current year range.  
```diff  
-Copyright 2005-2007 Daniel James.  
+Copyright (C) 2022-2024 Jean-Louis Leroy.  
```


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-10-24 02:07:56 UTC  
> Url: https://github.com/boostorg/openmethod/pull/41#issuecomment-3440447515  

## Welcome to [Codecov](https://codecov.io?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) :tada:  
  
Once you merge this PR into your default branch, you're all set! Codecov will compare coverage reports and display results in all future pull requests.  
  
Thanks for integrating Codecov - We've got you covered :open_umbrella:

---

## Review 3 [Approved]

> Username: joaquintides  
> Created_at: 2025-10-24 07:33:31 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/openmethod/pull/41#pullrequestreview-3374894980  

LGTM

---
