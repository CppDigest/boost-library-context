# #28 make n2216 resolution an opt-in [Merged]

> Username: jll63  
> Created at: 2025-06-07 22:20:56 UTC  
> Updated at: 2025-06-14 14:18:05 UTC  
> Merged at: 2025-06-09 12:11:49 UTC  
> Closed at: 2025-06-09 12:11:49 UTC  
> Url: https://github.com/boostorg/openmethod/pull/28  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-06-08 21:45:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/28#pullrequestreview-2908644288  

## Pull Request Overview  
  
This PR makes the N2216-based ambiguity resolution opt-in via a new `policies::n2216` policy, refactors call errors to carry argument type information, and updates tests to use a new `string_pair` helper instead of an internal `Types` enum.  
  
- Introduce `policies::n2216` and switch to a generic `call_error`/`ambiguous_error` mechanism  
- Update method dispatch logic and `method` internals to respect the new policy  
- Revamp tests: replace the old `matrices::Types` with `test_matrices::string_pair`  
  
### Reviewed Changes  
  
Copilot reviewed 8 out of 8 changed files in this pull request and generated 2 comments.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                             | Description                                                         |  
| ------------------------------------------------ | ------------------------------------------------------------------- |  
| test/test_util.hpp                               | Add `MAKE_STRING_CONSTANT` and `string_pair` helper for tests       |  
| test/test_n2216.cpp                              | New tests for the `n2216` policy and covariant return resolving     |  
| test/test_blackbox.cpp                           | Swap out old `matrices::Types` for `test_matrices::string_pair`, add throw-error policy tests |  
| include/boost/openmethod/registry.hpp            | Define new `struct n2216 : policy`                                  |  
| include/boost/openmethod/policies/throw_error_handler.hpp | Add `throw_error_handler` policy                                   |  
| include/boost/openmethod/detail/types.hpp        | Collapse `not_implemented_error` into `call_error` with derived `ambiguous_error` |  
| include/boost/openmethod/core.hpp                | Hook up `fn_ambiguous`, adjust `has_next()` to skip ambiguity when opted in |  
| include/boost/openmethod/compiler.hpp            | Wire up `ambiguous` overrider, count ambiguous cases, and skip them under `n2216` |  
</details>  
  
  
  
<details>  
<summary>Comments suppressed due to low confidence (1)</summary>  
  
**include/boost/openmethod/core.hpp:1000**  
* Moving `vp_type_ids` from an inline-static array to an instance member causes each `method` object to carry its own copy, increasing memory usage. If a shared static table per method is intended, consider restoring the `inline static` qualifier.  
```  
    type_id vp_type_ids[Arity];  
```  
</details>

📁 test/test_util.hpp

```diff
  32 | };
  33 | 
  34 |+ #define MAKE_STRING_CONSTANT(ID) static const std::string ID = #ID;
```

> Username: Copilot  
> Created_at: 2025-06-08 21:45:14 UTC  
> Url: https://github.com/boostorg/openmethod/pull/28#discussion_r2134834438  

[nitpick] Declaring a non-inline `static const std::string` in a header can cause multiple-definition linker errors. Consider marking it `inline static` under C++17 or later to ensure a single definition across translation units.  
```diff  
-#define MAKE_STRING_CONSTANT(ID) static const std::string ID = #ID;  
+#define MAKE_STRING_CONSTANT(ID) inline static const std::string ID = #ID;  
```


📁 test/test_blackbox.cpp

```diff
 375 |+     BOOST_TEST(report.ambiguous == 1u);
 376 |+     BOOST_CHECK_THROW(
 377 |+         times(diagonal_matrix(), diagonal_matrix()), ambiguous_error);
```

> Username: Copilot  
> Created_at: 2025-06-08 21:45:14 UTC  
> Url: https://github.com/boostorg/openmethod/pull/28#discussion_r2134834439  

The test refers to `ambiguous_error` without a namespace or using-directive. This symbol lives in `boost::openmethod::detail::ambiguous_error`, so it won’t be found here. Qualify the exception type or add a `using` for the correct namespace.  
```diff  
-        times(diagonal_matrix(), diagonal_matrix()), ambiguous_error);  
+        times(diagonal_matrix(), diagonal_matrix()), boost::openmethod::detail::ambiguous_error);  
```


---
