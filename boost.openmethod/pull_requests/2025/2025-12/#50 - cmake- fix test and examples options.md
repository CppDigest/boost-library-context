# #50 cmake: fix test and examples options [Closed]

> Username: jll63  
> Created at: 2025-12-11 22:58:41 UTC  
> Updated at: 2025-12-14 19:09:26 UTC  
> Closed at: 2025-12-14 19:08:38 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-12-11 23:05:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/50#pullrequestreview-3569658991  

## Pull request overview  
  
This PR fixes CMake configuration issues related to test and example build options. The changes make the default behavior more consistent and correctly nest example dependencies within the test infrastructure.  
  
Key changes:  
- Changed `BOOST_OPENMETHOD_BUILD_TESTS` default from `OFF` to `${BOOST_OPENMETHOD_IS_ROOT}` to match the examples option behavior  
- Moved example library dependency configuration inside the test conditional block  
- Reorganized examples section to be nested within the testing conditional, reflecting the actual dependency on test infrastructure  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 CMakeLists.txt

```diff
 185 |+     # Examples
 186 |+     if (BOOST_OPENMETHOD_BUILD_EXAMPLES)
 187 |+         enable_testing()
```

> Username: Copilot  
> Created_at: 2025-12-11 23:05:22 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50#discussion_r2612343868  

This call to `enable_testing()` is redundant as it's already called on line 179 within the same conditional block. The duplicate call should be removed.  
```diff  
-        enable_testing()  
```

---

📁 CMakeLists.txt

```diff
 187 |+         enable_testing()
 188 |+         add_subdirectory(doc/modules/ROOT/examples)
 189 |+     endif ()
```

> Username: Copilot  
> Created_at: 2025-12-11 23:05:22 UTC  
> Updated_at: 2025-12-11 23:05:23 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50#discussion_r2612343902  

Moving the examples section inside the testing conditional creates a confusing API where `BOOST_OPENMETHOD_BUILD_EXAMPLES` can be set to ON, but examples won't actually build unless `BUILD_TESTING` or `BOOST_OPENMETHOD_BUILD_TESTS` is also enabled. While the examples do depend on test infrastructure (they use `add_test()` and the `tests` target), consider either:  
1. Documenting this dependency in the option description on line 41, or  
2. Adding a check that warns or errors if `BOOST_OPENMETHOD_BUILD_EXAMPLES` is ON but testing is disabled, or  
3. Automatically enabling `BOOST_OPENMETHOD_BUILD_TESTS` when `BOOST_OPENMETHOD_BUILD_EXAMPLES` is ON.


---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2025-12-11 23:09:51 UTC  
> Updated_at: 2025-12-14 15:20:26 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50#issuecomment-3644173537  

An automated preview of the documentation is available at [https://50.openmethod.prtest3.cppalliance.org/libs/openmethod/doc/html/index.html](https://50.openmethod.prtest3.cppalliance.org/libs/openmethod/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-12-14 15:20:25 UTC

---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2025-12-12 00:00:08 UTC  
> Updated_at: 2025-12-14 19:09:26 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50#issuecomment-3644309110  

## [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/50?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:white_check_mark: All modified and coverable lines are covered by tests.  
:white_check_mark: Project coverage is 92.03%. Comparing base ([`dac3cf7`](https://app.codecov.io/gh/boostorg/openmethod/commit/dac3cf7087e70e8090768bfcf87fd24d57114ffe?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`3bb2414`](https://app.codecov.io/gh/boostorg/openmethod/commit/3bb241400a0a57116e85880cb5cab753fc440be9?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 8 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/openmethod/pull/50/graphs/tree.svg?width=650&height=150&src=pr&token=YLF1dcp5xA&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/openmethod/pull/50?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #50   +/-   ##  
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
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/openmethod/pull/50?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/openmethod/pull/50?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [764c0b9...3bb2414](https://app.codecov.io/gh/boostorg/openmethod/pull/50?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Review 4 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-12-13 19:24:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/50#pullrequestreview-3574527646  

## Pull request overview  
  
Copilot reviewed 1 out of 1 changed files in this pull request and generated 5 comments.  
  
  
  
  
  
---  
  
💡 <a href="/boostorg/openmethod/new/develop/.github/instructions?filename=*.instructions.md" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Add Copilot custom instructions</a> for smarter, more guided reviews. <a href="https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot" class="Link--inTextBlock" target="_blank" rel="noopener noreferrer">Learn how to get started</a>.

📁 CMakeLists.txt

```diff
 102 | if (NOT BOOST_OPENMETHOD_MRDOCS_BUILD)
 103 |     if (BUILD_TESTING OR BOOST_OPENMETHOD_BUILD_TESTS)
```

> Username: Copilot  
> Created_at: 2025-12-13 19:24:29 UTC  
> Updated_at: 2025-12-13 19:24:32 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50#discussion_r2616484087  

The condition `BUILD_TESTING OR BOOST_OPENMETHOD_BUILD_TESTS` is redundant. Since lines 46-48 ensure that BOOST_OPENMETHOD_BUILD_TESTS is ON whenever BUILD_TESTING is ON, checking BUILD_TESTING separately is unnecessary. Consider simplifying this to just check BOOST_OPENMETHOD_BUILD_TESTS for better maintainability.  
```diff  
+    if (BOOST_OPENMETHOD_BUILD_TESTS)  
```

---

📁 CMakeLists.txt

```diff
  66 |+         "BOOST_OPENMETHOD_BUILD_EXAMPLES requires BOOST_OPENMETHOD_BUILD_TESTS. Examples will not be built.")
  67 |+     set(BOOST_OPENMETHOD_BUILD_EXAMPLES OFF)
  68 |+ endif()
```

> Username: Copilot  
> Created_at: 2025-12-13 19:24:30 UTC  
> Updated_at: 2025-12-13 19:24:32 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50#discussion_r2616484094  

Inconsistent spacing in endif statement. Line 68 uses `endif()` without a space, while line 213 (also newly added in this PR) uses `endif ()` with a space. Consider standardizing on one style throughout the file for consistency.  
```diff  
-endif()  
+endif ()  
```

---

📁 CMakeLists.txt

```diff
 105 |+         if (BOOST_OPENMETHOD_BUILD_EXAMPLES)
 106 |+             set(BOOST_OPENMETHOD_EXAMPLE_LIBRARIES dll)
 107 |+         endif()
```

> Username: Copilot  
> Created_at: 2025-12-13 19:24:30 UTC  
> Updated_at: 2025-12-13 19:24:32 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50#discussion_r2616484095  

Inconsistent spacing in endif statement. Line 107 uses `endif()` without a space, while line 213 (also newly added in this PR) uses `endif ()` with a space. Consider standardizing on one style throughout the file for consistency.  
```diff  
-        endif()  
+        endif ()  
```

---

📁 CMakeLists.txt

```diff
  44 |+     ${BOOST_OPENMETHOD_IS_ROOT})
  45 |+ 
  46 |+ if (${BUILD_TESTING})
```

> Username: Copilot  
> Created_at: 2025-12-13 19:24:30 UTC  
> Updated_at: 2025-12-13 19:24:32 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50#discussion_r2616484098  

The condition should use `BUILD_TESTING` without the braces (i.e., `if (BUILD_TESTING)` instead of `if (${BUILD_TESTING})`). Using braces in if() conditions is not recommended in CMake as it can lead to unexpected behavior with undefined variables and is inconsistent with modern CMake best practices. Line 103 correctly uses the unbraced form.  
```diff  
-if (${BUILD_TESTING})  
+if (BUILD_TESTING)  
```

---

📁 CMakeLists.txt

```diff
  41 |+ option(
  42 |+     BOOST_OPENMETHOD_BUILD_TESTS
  43 |+     "Build boost::openmethod tests even if BUILD_TESTING is OFF"
```

> Username: Copilot  
> Created_at: 2025-12-13 19:24:31 UTC  
> Updated_at: 2025-12-13 19:24:32 UTC  
> Url: https://github.com/boostorg/openmethod/pull/50#discussion_r2616484106  

The option description is now misleading. The option no longer allows building tests "even if BUILD_TESTING is OFF" because lines 46-48 force BOOST_OPENMETHOD_BUILD_TESTS to ON when BUILD_TESTING is ON. The description should be updated to accurately reflect that this option enables tests by default when the project is root, but is overridden to ON when BUILD_TESTING is ON.  
```diff  
-    "Build boost::openmethod tests even if BUILD_TESTING is OFF"  
+    "Enable boost::openmethod tests by default when this is the root project; always ON if BUILD_TESTING is ON"  
```


---
