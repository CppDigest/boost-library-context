# #38 static checks for invalid method params [Merged]

> Username: jll63  
> Created at: 2025-06-30 22:05:26 UTC  
> Updated at: 2025-07-07 20:37:30 UTC  
> Merged at: 2025-07-01 12:53:12 UTC  
> Closed at: 2025-07-01 12:53:12 UTC  
> Url: https://github.com/boostorg/openmethod/pull/38  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-06-30 22:07:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/38#pullrequestreview-2972898553  

## Pull Request Overview  
  
This PR strengthens static checks for invalid method parameters and improves polymorphic behavior across the codebase. Key changes include:  
- Updating method parameter validation with new static_assert conditions.  
- Refactoring constructors and destructors in test files to use an injected output stream instead of hardcoded std::cout.  
- Adjustments in core.hpp and test files to accommodate a revised parameter order for vptr functions.  
  
### Reviewed Changes  
  
Copilot reviewed 6 out of 6 changed files in this pull request and generated 1 comment.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                                     | Description                                                     |  
| -------------------------------------------------------- | --------------------------------------------------------------- |  
| test/test_intrusive.cpp                                  | Updated intrusive tests to use std::ostream injection and added a new "meet" method. |  
| test/test_core.cpp                                       | Added static_asserts for valid_method_parameter and adjusted vptr tests. |  
| test/test_compiler.cpp                                   | Introduced virtual destructors for polymorphic base classes in tests. |  
| test/compile_fail_virtual_ptr_different_registries.cpp   | Added a test case to check mismatched registry usages.          |  
| test/compile_fail_bad_virtual_parameter.cpp              | Added a test case ensuring invalid virtual parameter usage fails. |  
| include/boost/openmethod/core.hpp                        | Updated template parameter order and added valid_method_parameter checks. |  
</details>

📁 test/test_intrusive.cpp

```diff
  86 |-     name = "Felix";
  87 |-     describe(*this, std::cout);
 100 |+     describe(*this, Cat::os);
```

> Username: Copilot  
> Created_at: 2025-06-30 22:07:38 UTC  
> Url: https://github.com/boostorg/openmethod/pull/38#discussion_r2176053893  

Using 'Cat::os' in the DomesticCat destructor may be ambiguous due to multiple inheritance (both Cat and Pet define an 'os' member). Consider using a more explicit approach or adding a comment to clarify which base member should be used.  
```diff  
-    describe(*this, Cat::os);  
+    // Use Cat::os explicitly to avoid ambiguity with Pet::os  
+    std::ostream& selected_os = Cat::os;  
+    describe(*this, selected_os);  
```


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-06-30 22:17:39 UTC  
> Updated_at: 2025-07-01 13:00:00 UTC  
> Url: https://github.com/boostorg/openmethod/pull/38#issuecomment-3020985597  

## [Codecov](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.30%. Comparing base [(`489f1d2`)](https://app.codecov.io/gh/jll63/Boost.OpenMethod/commit/489f1d27d8325298df50d99422551abf6bb20d7b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy) to head [(`6a9ccf1`)](https://app.codecov.io/gh/jll63/Boost.OpenMethod/commit/6a9ccf170be0c6b8fd9b8f8188c073eaa4cae28e?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy).  
> Report is 1 commits behind head on master.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38/graphs/tree.svg?width=650&height=150&src=pr&token=WOMDHC3NJD&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy)](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy)  
  
```diff  
@@            Coverage Diff             @@  
##           master      #38      +/-   ##  
==========================================  
- Coverage   93.31%   93.30%   -0.01%       
==========================================  
  Files          36       36                
  Lines        2917     2914       -3       
==========================================  
- Hits         2722     2719       -3       
  Misses        195      195                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy) | Coverage Δ | |  
|---|---|---|  
| [include/boost/openmethod/core.hpp](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?src=pr&el=tree&filepath=include%2Fboost%2Fopenmethod%2Fcore.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy#diff-aW5jbHVkZS9ib29zdC9vcGVubWV0aG9kL2NvcmUuaHBw) | `95.18% <ø> (ø)` | |  
| [test/test\_compiler.cpp](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?src=pr&el=tree&filepath=test%2Ftest_compiler.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy#diff-dGVzdC90ZXN0X2NvbXBpbGVyLmNwcA==) | `98.96% <ø> (ø)` | |  
| [test/test\_core.cpp](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?src=pr&el=tree&filepath=test%2Ftest_core.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy#diff-dGVzdC90ZXN0X2NvcmUuY3Bw) | `81.81% <ø> (ø)` | |  
| [test/test\_intrusive.cpp](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?src=pr&el=tree&filepath=test%2Ftest_intrusive.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy#diff-dGVzdC90ZXN0X2ludHJ1c2l2ZS5jcHA=) | `98.83% <100.00%> (+0.04%)` | :arrow_up: |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy). Last update [489f1d2...6a9ccf1](https://app.codecov.io/gh/jll63/Boost.OpenMethod/pull/38?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy).  
  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
