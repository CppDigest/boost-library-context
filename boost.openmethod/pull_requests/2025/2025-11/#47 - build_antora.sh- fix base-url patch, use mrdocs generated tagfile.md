# #47 build_antora.sh: fix base-url patch, use mrdocs generated tagfile [Closed]

> Username: jll63  
> Created at: 2025-11-23 16:02:03 UTC  
> Updated at: 2025-11-23 18:02:03 UTC  
> Closed at: 2025-11-23 18:01:53 UTC  
> Url: https://github.com/boostorg/openmethod/pull/47  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-11-23 16:03:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/47#pullrequestreview-3497871090  

## Pull request overview  
  
This PR fixes the base-url patching mechanism in the Antora documentation build script and switches to using mrdocs-generated tagfiles instead of manually maintained ones. The changes include:  
  
- Improved logic to detect repository and SHA information from CI environments (CircleCI and GitHub Actions)  
- Updated dependency versions for Antora and related packages  
- Removed manually maintained tagfile in favor of mrdocs-generated versions  
- Minor configuration updates to exclude additional symbols and fix base URL  
  
### Reviewed changes  
  
Copilot reviewed 6 out of 7 changed files in this pull request and generated 3 comments.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File | Description |  
| ---- | ----------- |  
| doc/build_antora.sh | Enhanced base-url detection logic to properly handle Boost superproject scenarios with improved environment variable handling |  
| doc/mrdocs.yml | Updated base-url from 'develop' to 'master' branch and added 'compiler' to exclude-symbols |  
| doc/antora.yml | Removed manual tagfile configuration entries, switching to mrdocs-generated tagfiles |  
| doc/package.json | Updated Antora and related package versions to latest compatible releases |  
| doc/package-lock.json | Regenerated lockfile reflecting updated dependencies |  
| doc/tagfiles/boost-openmethod-doxygen.tag.xml | Deleted manually maintained tagfile (907 lines removed) |  
| include/boost/openmethod/preamble.hpp | Added blank line for formatting consistency |  
</details>  
  
  
<details>  
<summary>Files not reviewed (1)</summary>  
  
* **doc/package-lock.json**: Language not supported  
</details>  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 doc/build_antora.sh

```diff
  51 |+       REPOSITORY="${ACCOUNT}/${LIB}"
  52 |+     fi
  53 |+     SHA=$(git -C "$BOOST_SRC_DIR/libs" ls-tree HEAD | grep openmethod | awk '{print $3}')
```

> Username: Copilot  
> Created_at: 2025-11-23 16:03:42 UTC  
> Updated_at: 2025-11-23 16:03:43 UTC  
> Url: https://github.com/boostorg/openmethod/pull/47#discussion_r2554174346  

The `grep openmethod` command is fragile and could match unintended lines if other entries contain 'openmethod' as a substring. Use `grep -w openmethod` or `grep '^[0-9]* tree [a-f0-9]* openmethod$'` to match only exact entries.  
```diff  
-    SHA=$(git -C "$BOOST_SRC_DIR/libs" ls-tree HEAD | grep openmethod | awk '{print $3}')  
+    SHA=$(git -C "$BOOST_SRC_DIR/libs" ls-tree HEAD | grep -w openmethod | awk '{print $3}')  
```

---

📁 doc/build_antora.sh

```diff
  42 |+ if [ -n "${BOOST_SRC_DIR:-}" ]; then
  43 |+   if [ -n "${CIRCLE_REPOSITORY_URL:-}" ]; then
  44 |+     if [[ "$CIRCLE_REPOSITORY_URL" =~ boostorg/boost(\.git)?$ ]]; then
```

> Username: Copilot  
> Created_at: 2025-11-23 16:03:43 UTC  
> Url: https://github.com/boostorg/openmethod/pull/47#discussion_r2554174352  

The regex pattern should be anchored more carefully. The pattern `boostorg/boost(\.git)?$` could match URLs like `https://github.com/user/boostorg/boost.git`. Consider using a more specific pattern like `[:/]boostorg/boost(\.git)?$` to ensure proper matching.  
```diff  
-    if [[ "$CIRCLE_REPOSITORY_URL" =~ boostorg/boost(\.git)?$ ]]; then  
+    if [[ "$CIRCLE_REPOSITORY_URL" =~ [:/]boostorg/boost(\.git)?$ ]]; then  
```

---

📁 doc/build_antora.sh

```diff
  51 |+       REPOSITORY="${ACCOUNT}/${LIB}"
  52 |+     fi
  53 |+     SHA=$(git -C "$BOOST_SRC_DIR/libs" ls-tree HEAD | grep openmethod | awk '{print $3}')
```

> Username: Copilot  
> Created_at: 2025-11-23 16:03:43 UTC  
> Updated_at: 2025-11-23 16:03:44 UTC  
> Url: https://github.com/boostorg/openmethod/pull/47#discussion_r2554174355  

This command will fail silently if the git command fails or if 'openmethod' is not found. Add error handling: check the exit status and ensure SHA is not empty before proceeding. Consider using `|| true` and then checking if SHA is empty, or use `set -o pipefail` to catch pipeline failures.


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-11-23 16:45:01 UTC  
> Updated_at: 2025-11-23 18:02:03 UTC  
> Url: https://github.com/boostorg/openmethod/pull/47#issuecomment-3568142325  

## [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/47?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 92.03%. Comparing base ([`dac3cf7`](https://app.codecov.io/gh/boostorg/openmethod/commit/dac3cf7087e70e8090768bfcf87fd24d57114ffe?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`5f9557e`](https://app.codecov.io/gh/boostorg/openmethod/commit/5f9557ea72e8d7ac39e7a5134e841159349d93f5?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 4 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/openmethod/pull/47/graphs/tree.svg?width=650&height=150&src=pr&token=YLF1dcp5xA&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/openmethod/pull/47?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #47   +/-   ##  
========================================  
  Coverage    92.03%   92.03%             
========================================  
  Files           40       40             
  Lines         2838     2838             
  Branches      1354     1354             
========================================  
  Hits          2612     2612             
  Misses         187      187             
  Partials        39       39             
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/openmethod/pull/47?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/openmethod/preamble.hpp](https://app.codecov.io/gh/boostorg/openmethod/pull/47?src=pr&el=tree&filepath=include%2Fboost%2Fopenmethod%2Fpreamble.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9vcGVubWV0aG9kL3ByZWFtYmxlLmhwcA==) | `71.73% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/openmethod/pull/47?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/47?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [b8da0a1...5f9557e](https://app.codecov.io/gh/boostorg/openmethod/pull/47?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
