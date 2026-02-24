# #46 small initialize/finalize adjustments [Closed]

> Username: jll63  
> Created at: 2025-11-15 21:37:07 UTC  
> Updated at: 2025-11-18 20:06:01 UTC  
> Closed at: 2025-11-18 18:57:29 UTC  
> Url: https://github.com/boostorg/openmethod/pull/46  

* Make `initialize` and `finalize` symmetric.  
* Hide the functions in `registry`  
* `initialize` should not go in `aliases`, as that is for frequently used names only and `initialize` is typically called only from `main`.

---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-11-15 21:41:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/46#pullrequestreview-3468393128  

## Pull Request Overview  
  
This PR refactors the `initialize` and `finalize` functions to make them symmetric by providing template-based free functions for both. The PR also hides the `registry::initialize` and `registry::finalize` member functions from public documentation and removes `initialize` from the `aliases` namespace since it's typically called only once from main.  
  
Key changes:  
- Added a templated free function `finalize<Registry>()` to match the existing `initialize<Registry>()` pattern  
- Removed `initialize` from the `boost::openmethod::aliases` namespace  
- Moved documentation from `registry::finalize` member function to the new free function `finalize`  
  
### Reviewed Changes  
  
Copilot reviewed 7 out of 7 changed files in this pull request and generated 1 comment.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File | Description |  
| ---- | ----------- |  
| test/test_dispatch.cpp | Updated test to use new `finalize<test_registry>()` syntax instead of `test_registry::finalize()` |  
| include/boost/openmethod/preamble.hpp | Removed documentation comments from `registry::finalize` member function |  
| include/boost/openmethod/initialize.hpp | Added templated `finalize` free function with comprehensive documentation; removed `initialize` from aliases namespace |  
| doc/mrdocs.yml | Added exclusions to hide `registry::initialize` and `registry::finalize` from generated documentation |  
| doc/modules/ROOT/examples/shared_libs/static_main.cpp | Updated to use fully qualified `boost::openmethod::initialize()` instead of unqualified `initialize()` |  
| doc/modules/ROOT/examples/shared_libs/indirect_main.cpp | Updated all three calls to use fully qualified `boost::openmethod::initialize()` |  
| doc/modules/ROOT/examples/shared_libs/dynamic_main.cpp | Updated all three calls to use fully qualified `boost::openmethod::initialize()` |  
</details>  
  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 include/boost/openmethod/initialize.hpp

```diff
1676 |+ //! @param options Zero or more option objects.
1677 |+ template<class Registry = BOOST_OPENMETHOD_DEFAULT_REGISTRY, class... Options>
1678 |+ inline auto finalize(Options... opts) -> void {
```

> Username: Copilot  
> Created_at: 2025-11-15 21:41:08 UTC  
> Url: https://github.com/boostorg/openmethod/pull/46#discussion_r2530174923  

The `finalize` function should use perfect forwarding for consistency with the `initialize` function. The parameter should be `Options&&... opts` and the call should use `std::forward<Options>(opts)...`. Currently, `initialize` at line 1613 uses `Options&&... options` with perfect forwarding, but `finalize` does not.  
```diff  
-inline auto finalize(Options... opts) -> void {  
+inline auto finalize(Options&&... opts) -> void {  
```


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-11-15 22:24:03 UTC  
> Updated_at: 2025-11-18 20:06:01 UTC  
> Url: https://github.com/boostorg/openmethod/pull/46#issuecomment-3536978342  

## [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/46?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 92.03%. Comparing base ([`f7d47fb`](https://app.codecov.io/gh/boostorg/openmethod/commit/f7d47fbd359c4f9c85dc5f720265d0c1ee1657a6?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`b82992b`](https://app.codecov.io/gh/boostorg/openmethod/commit/b82992b09944db23a200b91daf3c48903a464852?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 10 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/openmethod/pull/46/graphs/tree.svg?width=650&height=150&src=pr&token=YLF1dcp5xA&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/openmethod/pull/46?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #46      +/-   ##  
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
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/openmethod/pull/46?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/openmethod/initialize.hpp](https://app.codecov.io/gh/boostorg/openmethod/pull/46?src=pr&el=tree&filepath=include%2Fboost%2Fopenmethod%2Finitialize.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9vcGVubWV0aG9kL2luaXRpYWxpemUuaHBw) | `89.05% <ø> (-4.89%)` | :arrow_down: |  
| [include/boost/openmethod/preamble.hpp](https://app.codecov.io/gh/boostorg/openmethod/pull/46?src=pr&el=tree&filepath=include%2Fboost%2Fopenmethod%2Fpreamble.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9vcGVubWV0aG9kL3ByZWFtYmxlLmhwcA==) | `71.73% <ø> (+0.62%)` | :arrow_up: |  
| [test/test\_dispatch.cpp](https://app.codecov.io/gh/boostorg/openmethod/pull/46?src=pr&el=tree&filepath=test%2Ftest_dispatch.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2Rpc3BhdGNoLmNwcA==) | `99.34% <ø> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/openmethod/pull/46/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/openmethod/pull/46?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/46?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [83bc8d2...b82992b](https://app.codecov.io/gh/boostorg/openmethod/pull/46?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
