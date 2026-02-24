# #31 improve error handling [Merged]

> Username: jll63  
> Created at: 2025-06-19 21:14:53 UTC  
> Updated at: 2025-06-20 22:26:52 UTC  
> Merged at: 2025-06-19 21:40:20 UTC  
> Closed at: 2025-06-19 21:40:20 UTC  
> Url: https://github.com/boostorg/openmethod/pull/31  

* check that `initialize` was called (debug variants only)  
* `throw_error_handler` should throw objects derived from `std::runtime_error`  
* policy helpers

---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-06-19 21:16:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/31#pullrequestreview-2944036650  

## Pull Request Overview  
  
This PR improves error handling by ensuring that error signals are appropriately checked and reported and that errors thrown are derived from std::runtime_error. Key changes include:  
- Replacing the previous type_mismatch_error with final_error in virtual pointer error handling.  
- Updating throw_error_handler to wrap errors in a std::runtime_error‐derived exception.  
- Adding an explicit Registry initialization check and expanding test coverage for error conditions.  
  
### Reviewed Changes  
  
Copilot reviewed 12 out of 12 changed files in this pull request and generated 1 comment.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File | Description |  
| ---- | ----------- |  
| test/test_virtual_ptr_value_semantics.cpp | Updated error type checks and assertions in the virtual_ptr final error test case. |  
| test/test_util.hpp | Modified the test registry template to support additional policies. |  
| test/test_runtime_errors.cpp | Expanded tests to cover not_initialized_error, unknown_class_error, and ambiguous_error scenarios. |  
| test/test_dispatch.cpp | Removed redundant error handling tests to align with updated error handling design. |  
| include/boost/openmethod/registry.hpp | Introduced an initialization flag and updated check_initialized() for error handling. |  
| include/boost/openmethod/policies/vectored_error_handler.hpp | Changed error variant inspection from type_mismatch_error to final_error. |  
| include/boost/openmethod/policies/throw_error_handler.hpp | Now wraps thrown errors in a std::runtime_error‐derived wrapper for consistency. |  
| include/boost/openmethod/policies/default_error_handler.hpp | Reordered and updated the error variant types processed by the default error handler. |  
| include/boost/openmethod/detail/types.hpp | Added polymorphic error types including not_initialized_error and final_error, replacing type_mismatch_error. |  
| include/boost/openmethod/detail/trace.hpp | Updated trace invocation to check for the new has_trace flag. |  
| include/boost/openmethod/core.hpp | Added calls to Registry::check_initialized() and updated error handling in virtual_ptr. |  
| include/boost/openmethod/compiler.hpp | Marks Registry as initialized once compilation and table installation are complete. |  
</details>  
  
  
  
<details>  
<summary>Comments suppressed due to low confidence (1)</summary>  
  
**include/boost/openmethod/policies/throw_error_handler.hpp:23**  
* [nitpick] Consider adding a comment explaining the purpose and design of this wrapper struct, particularly addressing the multiple inheritance from Error and std::runtime_error, to aid future maintainers in understanding this error wrapping mechanism.  
```  
            struct wrapper : Error, std::runtime_error {  
```  
</details>

📁 include/boost/openmethod/registry.hpp

```diff
 172 |+     using registry_type = registry;
 173 |+ 
 174 |+     inline static bool initialized;
```

> Username: Copilot  
> Created_at: 2025-06-19 21:16:26 UTC  
> Url: https://github.com/boostorg/openmethod/pull/31#discussion_r2157668305  

Consider explicitly initializing 'initialized' to false (e.g., 'inline static bool initialized = false;') to improve clarity and ensure future maintainers understand the intended default state.  
```diff  
-    inline static bool initialized;  
+    inline static bool initialized = false;  
```


---
