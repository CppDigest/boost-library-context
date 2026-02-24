# #52 remove extra semicolons [Merged]

> Username: jll63  
> Created at: 2025-12-14 20:42:21 UTC  
> Updated at: 2025-12-26 02:56:50 UTC  
> Merged at: 2025-12-18 01:12:53 UTC  
> Closed at: 2025-12-18 01:12:53 UTC  
> Url: https://github.com/boostorg/openmethod/pull/52  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-12-14 20:44:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/52#pullrequestreview-3575821039  

## Pull request overview  
  
This PR aims to remove extra semicolons from the codebase. However, while some changes are valid (removing unnecessary semicolons after function definitions), **the PR incorrectly removes required semicolons from macro definitions**, which will break compilation.  
  
- Correctly removes unnecessary semicolons after function body closing braces in non-macro contexts  
- Reformats trailing return type declarations for better readability    
- **Incorrectly removes required semicolons from three macro definitions**  
  
### Reviewed changes  
  
Copilot reviewed 3 out of 3 changed files in this pull request and generated 3 comments.  
  
| File | Description |  
| ---- | ----------- |  
| `include/boost/openmethod/policies/vptr_vector.hpp` | Correctly removes unnecessary semicolon after `finalize()` function body |  
| `include/boost/openmethod/macros.hpp` | **Incorrectly removes required semicolons from macro definitions at lines 82, 91, and 141; also reformats trailing return types** |  
| `include/boost/openmethod/detail/static_list.hpp` | Correctly removes unnecessary semicolons after friend operator function bodies in iterator classes |  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 include/boost/openmethod/macros.hpp

```diff
  80 |+     }                                                                          \
  81 |+     template<typename...>                                                      \
  82 |+     struct BOOST_OPENMETHOD_OVERRIDERS(NAME)
```

> Username: Copilot  
> Created_at: 2025-12-14 20:44:21 UTC  
> Updated_at: 2025-12-14 20:44:22 UTC  
> Url: https://github.com/boostorg/openmethod/pull/52#discussion_r2617471114  

The semicolon at the end of this struct declaration should not be removed. This is part of a macro definition (BOOST_OPENMETHOD), and the struct declaration needs to end with a semicolon for proper syntax. Users of this macro will not add a semicolon after the macro invocation since the macro previously provided it.  
```diff  
-    struct BOOST_OPENMETHOD_OVERRIDERS(NAME)  
+    struct BOOST_OPENMETHOD_OVERRIDERS(NAME);  
```

---

📁 include/boost/openmethod/macros.hpp

```diff
  90 |             decltype(BOOST_OPENMETHOD_GUIDE(NAME)(std::declval<A>()...));      \
  91 |-     };
  91 |+     }
```

> Username: Copilot  
> Created_at: 2025-12-14 20:44:21 UTC  
> Updated_at: 2025-12-14 20:44:22 UTC  
> Url: https://github.com/boostorg/openmethod/pull/52#discussion_r2617471115  

The semicolon at the end of this struct definition should not be removed. This is part of a macro definition (BOOST_OPENMETHOD_DETAIL_LOCATE_METHOD), and the struct definition needs to end with a semicolon for proper syntax. Users of this macro will expect the semicolon to be included in the expansion.  
```diff  
-    }  
+    };  
```

---

📁 include/boost/openmethod/macros.hpp

```diff
 140 | #define BOOST_OPENMETHOD_CLASSES(...)                                          \
 139 |-     BOOST_OPENMETHOD_REGISTER(::boost::openmethod::use_classes<__VA_ARGS__>);
 141 |+     BOOST_OPENMETHOD_REGISTER(::boost::openmethod::use_classes<__VA_ARGS__>)
```

> Username: Copilot  
> Created_at: 2025-12-14 20:44:21 UTC  
> Updated_at: 2025-12-14 20:44:22 UTC  
> Url: https://github.com/boostorg/openmethod/pull/52#discussion_r2617471116  

The semicolon at the end of this statement should not be removed. This is part of a macro definition (BOOST_OPENMETHOD_CLASSES), and the statement needs to end with a semicolon for proper syntax. Users of this macro will not add a semicolon after the macro invocation since the macro previously provided it.  
```diff  
-    BOOST_OPENMETHOD_REGISTER(::boost::openmethod::use_classes<__VA_ARGS__>)  
+    BOOST_OPENMETHOD_REGISTER(::boost::openmethod::use_classes<__VA_ARGS__>);  
```


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-12-14 20:50:13 UTC  
> Url: https://github.com/boostorg/openmethod/pull/52#issuecomment-3652078050  

An automated preview of the documentation is available at [https://52.openmethod.prtest3.cppalliance.org/libs/openmethod/doc/html/index.html](https://52.openmethod.prtest3.cppalliance.org/libs/openmethod/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-12-14 20:50:12 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-12-14 21:39:46 UTC  
> Updated_at: 2025-12-18 01:13:18 UTC  
> Url: https://github.com/boostorg/openmethod/pull/52#issuecomment-3652223457  

## [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/52?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 92.02%. Comparing base ([`e0ead3b`](https://app.codecov.io/gh/boostorg/openmethod/commit/e0ead3bf7106f614bb8ba8419e0c35fdd95c2ea4?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`50a5fac`](https://app.codecov.io/gh/boostorg/openmethod/commit/50a5fac9355cc4fdc0c5acf0a81ed01e0d135f6a?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/openmethod/pull/52/graphs/tree.svg?width=650&height=150&src=pr&token=YLF1dcp5xA&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/openmethod/pull/52?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #52      +/-   ##  
===========================================  
- Coverage    92.03%   92.02%   -0.01%       
===========================================  
  Files           40       40                
  Lines         2838     2835       -3       
  Branches      1354     1354                
===========================================  
- Hits          2612     2609       -3       
  Misses         187      187                
  Partials        39       39                
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/openmethod/pull/52?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/openmethod/detail/static\_list.hpp](https://app.codecov.io/gh/boostorg/openmethod/pull/52?src=pr&el=tree&filepath=include%2Fboost%2Fopenmethod%2Fdetail%2Fstatic_list.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9vcGVubWV0aG9kL2RldGFpbC9zdGF0aWNfbGlzdC5ocHA=) | `96.96% <ø> (-0.14%)` | :arrow_down: |  
| [include/boost/openmethod/policies/vptr\_vector.hpp](https://app.codecov.io/gh/boostorg/openmethod/pull/52?src=pr&el=tree&filepath=include%2Fboost%2Fopenmethod%2Fpolicies%2Fvptr_vector.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9vcGVubWV0aG9kL3BvbGljaWVzL3ZwdHJfdmVjdG9yLmhwcA==) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/openmethod/pull/52?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/52?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [ef48e48...50a5fac](https://app.codecov.io/gh/boostorg/openmethod/pull/52?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---
