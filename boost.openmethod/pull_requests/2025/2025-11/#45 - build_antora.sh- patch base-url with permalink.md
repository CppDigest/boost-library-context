# #45 build_antora.sh: patch base-url with permalink [Merged]

> Username: jll63  
> Created at: 2025-11-03 23:11:54 UTC  
> Updated at: 2025-11-18 18:56:52 UTC  
> Merged at: 2025-11-16 16:34:40 UTC  
> Closed at: 2025-11-16 16:34:40 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45  

> I’d just suggest adding a few checks and fallback values there (from [Slack](https://cpplang.slack.com/archives/C0508A7LWUV/p1761417941768489))  
  
Not sure what a fallback value should be, although there is one: if the script doesn't recognize the context, it leaves `base-url` alone, that is, currently, it links to the `release` branch.  
  
@pdimov for awareness

---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-11-03 23:20:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/45#pullrequestreview-3413316808  

## Pull Request Overview  
  
This PR adds CI environment detection and dynamic base URL configuration to the Antora documentation build process, and removes a branch restriction from the GitHub Pages deployment workflow.  
  
- Detects CircleCI and GitHub CI environments to set repository-specific base URLs  
- Modifies and restores `mrdocs.yml` configuration file during the build process  
- Allows GitHub Pages deployment for any branch (previously restricted to `feature/doc`)  
  
### Reviewed Changes  
  
Copilot reviewed 2 out of 2 changed files in this pull request and generated 3 comments.  
  
| File | Description |  
| ---- | ----------- |  
| doc/build_antora.sh | Adds CI environment detection logic to dynamically set base URLs and implements config file backup/restore mechanism |  
| .github/workflows/ci.yml | Removes branch name restriction from GitHub Pages deployment condition |  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 doc/build_antora.sh

```diff
  58 | done
  59 | 
  60 |+ if [ -n "${BASE_URL:-}" ]; then
```

> Username: Copilot  
> Created_at: 2025-11-03 23:20:15 UTC  
> Updated_at: 2025-11-03 23:20:17 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2488136326  

Variable name mismatch: the condition checks `BASE_URL` but the variable set earlier is `base_url` (lowercase). This causes the restore logic to never execute. Change to `if [ -n \"${base_url:-}\" ]; then` to match the variable defined on line 37.  
```diff  
-if [ -n "${BASE_URL:-}" ]; then  
+if [ -n "${base_url:-}" ]; then  
```

---

📁 doc/build_antora.sh

```diff
  42 |+   perl -i -pe 's{^\s*base-url:.*$}{base-url: '"$base_url"'}' mrdocs.yml
  43 |+ fi
  44 |+ 
```

> Username: Copilot  
> Created_at: 2025-11-03 23:20:16 UTC  
> Updated_at: 2025-11-03 23:20:18 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2488136352  

The `base_url` variable is set unconditionally even when `repository` and `sha` are undefined (i.e., when neither CIRCLE_REPOSITORY_URL nor GITHUB_REPOSITORY is set). This results in an invalid URL `https://github.com//blob//`. The assignment should be moved inside the conditional block or wrapped in a check for non-empty `repository` and `sha`.  
```diff  
+if [ -n "${repository}" ] && [ -n "${sha}" ]; then  
+  base_url="https://github.com/${repository}/blob/${sha}/"  
+  echo "Setting base-url to $base_url"  
+  cp mrdocs.yml mrdocs.yml.bak  
+  perl -i -pe 's{^\s*base-url:.*$}{base-url: '"$base_url"'}' mrdocs.yml  
+fi  
```

---

📁 doc/build_antora.sh

```diff
  27 |+   account="${CIRCLE_REPOSITORY_URL#*:}"
  28 |+   account="${account%%/*}"
  29 |+   lib=$(basename $(git rev-parse --show-toplevel))
```

> Username: Copilot  
> Created_at: 2025-11-03 23:20:16 UTC  
> Updated_at: 2025-11-03 23:20:18 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2488136362  

Unquoted command substitution could cause word splitting issues if the repository path contains spaces. Use `lib=$(basename \"$(git rev-parse --show-toplevel)\")` to properly handle paths with special characters.  
```diff  
-  lib=$(basename $(git rev-parse --show-toplevel))  
+  lib=$(basename "$(git rev-parse --show-toplevel)")  
```


---

## Comment 2

> Username: alandefreitas  
> Created_at: 2025-11-03 23:35:06 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#issuecomment-3483023228  

> Not sure what a fallback value should be  
  
Hey! 😊 I was thinking maybe:  
  
* **"boostorg"** for ORG (since it looks like ORG isn’t used in the script anymore — not totally sure though, maybe **"boostorg/openmethod"** for the whole repo)  
* **"master"** instead of the SHA, in case that specific SHA can’t be found  
  
But of course, you’d need to check if that really makes sense 👍❤️

---

## Comment 3

> Username: alandefreitas  
> Created_at: 2025-11-03 23:36:10 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#issuecomment-3483026354  

These changes are great, by the way 😊 I can totally see a lot of people replicating this logic

---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2025-11-03 23:47:38 UTC  
> Updated_at: 2025-11-16 16:34:56 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#issuecomment-3483050315  

## [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/45?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `0%` with `1 line` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 92.03%. Comparing base ([`f7d47fb`](https://app.codecov.io/gh/boostorg/openmethod/commit/f7d47fbd359c4f9c85dc5f720265d0c1ee1657a6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`dac3cf7`](https://app.codecov.io/gh/boostorg/openmethod/commit/dac3cf7087e70e8090768bfcf87fd24d57114ffe?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 10 commits behind head on develop.  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/openmethod/pull/45?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Patch % | Lines |  
|---|---|---|  
| [test/test\_virtual\_ptr\_doc.cpp](https://app.codecov.io/gh/boostorg/openmethod/pull/45?src=pr&el=tree&filepath=test%2Ftest_virtual_ptr_doc.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3ZpcnR1YWxfcHRyX2RvYy5jcHA=) | 0.00% | [1 Missing :warning: ](https://app.codecov.io/gh/boostorg/openmethod/pull/45?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) |  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/openmethod/pull/45/graphs/tree.svg?width=650&height=150&src=pr&token=YLF1dcp5xA&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/openmethod/pull/45?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #45      +/-   ##  
===========================================  
- Coverage    93.03%   92.03%   -1.00%       
===========================================  
  Files           40       40                
  Lines         2713     2838     +125       
  Branches      1226     1354     +128       
===========================================  
+ Hits          2524     2612      +88       
- Misses         159      187      +28       
- Partials        30       39       +9       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/openmethod/pull/45?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_virtual\_ptr\_doc.cpp](https://app.codecov.io/gh/boostorg/openmethod/pull/45?src=pr&el=tree&filepath=test%2Ftest_virtual_ptr_doc.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X3ZpcnR1YWxfcHRyX2RvYy5jcHA=) | `91.07% <0.00%> (-3.53%)` | :arrow_down: |  
  
... and [5 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/openmethod/pull/45/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/openmethod/pull/45?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/45?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [83bc8d2...dac3cf7](https://app.codecov.io/gh/boostorg/openmethod/pull/45?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 5 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-11-09 14:27:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/45#pullrequestreview-3440066647  

## Pull Request Overview  
  
Copilot reviewed 3 out of 3 changed files in this pull request and generated 3 comments.  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 doc/modules/ROOT/pages/ref_headers.adoc

```diff
  47 |+ 
  48 |+ [#initialize]
  49 |+ ### link:{{BASE_URL}}/include/boost/initialize.hpp[<boost/initialize.hpp>]
```

> Username: Copilot  
> Created_at: 2025-11-09 14:27:11 UTC  
> Updated_at: 2025-11-09 14:27:12 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2508090386  

The path is incorrect. Based on line 13 which references `<boost/openmethod/initialize.hpp>`, this should be `boost/openmethod/initialize.hpp` not `boost/initialize.hpp`. The `/openmethod/` part is missing from the path.  
```diff  
-### link:{{BASE_URL}}/include/boost/initialize.hpp[<boost/initialize.hpp>]  
+### link:{{BASE_URL}}/include/boost/openmethod/initialize.hpp[<boost/openmethod/initialize.hpp>]  
```

---

📁 doc/modules/ROOT/pages/ref_headers.adoc

```diff
  63 |+ ### link:{{BASE_URL}}/include/boost/openmethod/interop/std_unique_ptr.hpp[<boost/openmethod/interop/std_unique_ptr.hpp>]
  64 |+ 
  65 |+ Provides a `virtual_traits` specialization that make it possible to use a
```

> Username: Copilot  
> Created_at: 2025-11-09 14:27:11 UTC  
> Updated_at: 2025-11-09 14:27:12 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2508090391  

Subject-verb agreement error: 'specialization' is singular, so it should be 'makes' not 'make'.  
```diff  
-Provides a `virtual_traits` specialization that make it possible to use a  
+Provides a `virtual_traits` specialization that makes it possible to use a  
+`std::shared_ptr` in place of a raw pointer or reference in virtual parameters.  
+[#std_unique_ptr]  
+### link:{{BASE_URL}}/include/boost/openmethod/interop/std_unique_ptr.hpp[<boost/openmethod/interop/std_unique_ptr.hpp>]  
+Provides a `virtual_traits` specialization that makes it possible to use a  
```

---

📁 doc/modules/ROOT/pages/ref_headers.adoc

```diff
  63 |+ ### link:{{BASE_URL}}/include/boost/openmethod/interop/std_unique_ptr.hpp[<boost/openmethod/interop/std_unique_ptr.hpp>]
  64 |+ 
  65 |+ Provides a `virtual_traits` specialization that make it possible to use a
```

> Username: Copilot  
> Created_at: 2025-11-09 14:27:11 UTC  
> Updated_at: 2025-11-09 14:27:12 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2508090395  

Subject-verb agreement error: 'specialization' is singular, so it should be 'makes' not 'make'.  
```diff  
-Provides a `virtual_traits` specialization that make it possible to use a  
+Provides a `virtual_traits` specialization that makes it possible to use a  
+`std::shared_ptr` in place of a raw pointer or reference in virtual parameters.  
+[#std_unique_ptr]  
+### link:{{BASE_URL}}/include/boost/openmethod/interop/std_unique_ptr.hpp[<boost/openmethod/interop/std_unique_ptr.hpp>]  
+Provides a `virtual_traits` specialization that makes it possible to use a  
```


---

## Review 6 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-11-09 14:50:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/45#pullrequestreview-3440076450  

## Pull Request Overview  
  
Copilot reviewed 3 out of 3 changed files in this pull request and generated 1 comment.  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 doc/build_antora.sh

```diff
  35 |+ fi
  36 |+ 
  37 |+ if [ -n "${repository}" ] && -n "${sha}" ]; then
```

> Username: Copilot  
> Created_at: 2025-11-09 14:50:52 UTC  
> Updated_at: 2025-11-09 14:50:53 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2508102408  

Missing opening bracket before the second condition. Should be `[ -n \"${sha}\" ]` instead of `-n \"${sha}\"`. This will cause the script to fail with a syntax error.  
```diff  
-if [ -n "${repository}" ] && -n "${sha}" ]; then  
+if [ -n "${repository}" ] && [ -n "${sha}" ]; then  
```


---

## Review 7 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-11-09 15:34:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/45#pullrequestreview-3440094471  

## Pull Request Overview  
  
Copilot reviewed 14 out of 14 changed files in this pull request and generated no new comments.  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

---

## Review 8 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-11-09 16:24:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/45#pullrequestreview-3440121686  

## Pull Request Overview  
  
Copilot reviewed 14 out of 14 changed files in this pull request and generated no new comments.  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

---

## Review 9 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-11-15 20:51:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/45#pullrequestreview-3468350137  

## Pull Request Overview  
  
Copilot reviewed 14 out of 14 changed files in this pull request and generated 1 comment.  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 doc/build_antora.sh

```diff
  39 |+   echo "Setting base-url to $base_url"
  40 |+   cp mrdocs.yml mrdocs.yml.bak
  41 |+   perl -i -pe 's{^\s*base-url:.*$}{base-url: '"$base_url/"'}' mrdocs.yml
```

> Username: Copilot  
> Created_at: 2025-11-15 20:51:36 UTC  
> Updated_at: 2025-11-15 20:51:37 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2530135546  

The trailing slash added here will cause double slashes in the final URLs. The templates use `{{BASE_URL}}/include/...`, so after substitution this becomes `https://github.com/...//include/...`. Remove the trailing slash from the perl substitution: change `base-url: '"$base_url/"'` to `base-url: '"$base_url"'`.  
```diff  
-  perl -i -pe 's{^\s*base-url:.*$}{base-url: '"$base_url/"'}' mrdocs.yml  
+  perl -i -pe 's{^\s*base-url:.*$}{base-url: '"$base_url"'}' mrdocs.yml  
```

> Username: jll63  
> Created_at: 2025-11-15 21:28:41 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2530154438  

Wrong, MrDocs needs that slash.


---

## Review 10 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-11-15 21:50:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/45#pullrequestreview-3468423512  

## Pull Request Overview  
  
Copilot reviewed 15 out of 15 changed files in this pull request and generated 2 comments.  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 doc/build_antora.sh

```diff
  39 |+   echo "Setting base-url to $base_url"
  40 |+   cp mrdocs.yml mrdocs.yml.bak
  41 |+   perl -i -pe 's{^\s*base-url:.*$}{base-url: '"$base_url/"'}' mrdocs.yml
```

> Username: Copilot  
> Created_at: 2025-11-15 21:50:58 UTC  
> Updated_at: 2025-11-15 21:50:59 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2530205206  

Inconsistent trailing slash handling for `base_url`. Line 41 adds a trailing slash when writing to `mrdocs.yml` (`"$base_url/"`), but line 66 uses `$base_url` without the trailing slash for HTML substitution. Since the documentation files use paths like `{{BASE_URL}}/include/...`, using `$base_url` (without trailing slash) in line 66 is correct. However, line 41 should not add the trailing slash to maintain consistency. Consider changing line 41 to: `perl -i -pe 's{^\s*base-url:.*$}{base-url: '"$base_url"'}' mrdocs.yml`  
```diff  
-  perl -i -pe 's{^\s*base-url:.*$}{base-url: '"$base_url/"'}' mrdocs.yml  
+  perl -i -pe 's{^\s*base-url:.*$}{base-url: '"$base_url"'}' mrdocs.yml  
```


📁 doc/modules/ROOT/pages/ref_headers.adoc

```diff
  47 |+ 
  48 |+ [#initialize]
  49 |+ ### link:{{BASE_URL}}/include/boost/initialize.hpp[<boost/initialize.hpp>]
```

> Username: Copilot  
> Created_at: 2025-11-15 21:50:58 UTC  
> Updated_at: 2025-11-15 21:50:59 UTC  
> Url: https://github.com/boostorg/openmethod/pull/45#discussion_r2530205242  

The path should be `<boost/openmethod/initialize.hpp>` instead of `<boost/initialize.hpp>` to match the correct header location.  
```diff  
-### link:{{BASE_URL}}/include/boost/initialize.hpp[<boost/initialize.hpp>]  
+### link:{{BASE_URL}}/include/boost/openmethod/initialize.hpp[<boost/openmethod/initialize.hpp>]  
```


---
