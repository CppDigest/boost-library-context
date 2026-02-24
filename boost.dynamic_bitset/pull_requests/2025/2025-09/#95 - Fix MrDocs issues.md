# #95 Fix MrDocs issues [Merged]

> Username: alandefreitas  
> Created at: 2025-09-05 01:11:22 UTC  
> Updated at: 2025-09-05 17:27:20 UTC  
> Merged at: 2025-09-05 17:26:22 UTC  
> Closed at: 2025-09-05 17:26:22 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/95  

The PR fixed problems related to MrDocs:  
  
- CMakeLists.txt supports being used as the root script. Mrdocs calls cmakelists.txt as the root build script instead of the boost superproject script. When the build script is in the CMake root source directory, we need to include the Boost targets so that the include paths are found. Otherwise, you'll get compilation errors because of missing headers. In root mode, you'll get:  
    - a BOOST_SRC_DIR option that can be used to set the directory with other boost libraries (the default being ../..)  
    - Some logic to check if this boost source dir is valid  
    - And logic to include this boost dir as a subproject, only including the dynamicbitset dependencies and excluding dynamicbitset itself.  
- The mrdocs config is set to multipage because:  
    - The single page is immense. No human can read that.   
    - Even loading it was taking too long.   
    - Also, the Antora extension only supports multipage for now (IIRC), so any tests with single pages aren't going to be used.   
    - The recursive index page is looking pretty good for this library  
  
Feel free to change anything you don't like and treat anything here as just a suggestion.

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2025-09-05 15:35:31 UTC  
> Updated_at: 2025-09-05 17:26:34 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/95#issuecomment-3258810299  

## [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/95?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 94.28%. Comparing base ([`ead1fcb`](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/ead1fcb95cd9158c17a9a9f6998cfd91b68ed52f?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`e78467a`](https://app.codecov.io/gh/boostorg/dynamic_bitset/commit/e78467a113ebbadfbb61ce93a9ffc31dac84d8ef?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 2 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/95/graphs/tree.svg?width=650&height=150&src=pr&token=PVG5jth1ez&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/95?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #95      +/-   ##  
===========================================  
- Coverage    94.91%   94.28%   -0.63%       
===========================================  
  Files            5        4       -1       
  Lines          668      490     -178       
  Branches       211        0     -211       
===========================================  
- Hits           634      462     -172       
+ Misses          33       28       -5       
+ Partials         1        0       -1       
```  
[see 4 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/95/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/95?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/95?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ead1fcb...e78467a](https://app.codecov.io/gh/boostorg/dynamic_bitset/pull/95?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 2

> Username: gennaroprota  
> Created_at: 2025-09-05 17:27:20 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/95#issuecomment-3259215186  

Merged, thanks.

---
