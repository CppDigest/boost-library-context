# #26 rework deferred custom RTTI, change `type_id` to `const void*` [Merged]

> Username: jll63  
> Created at: 2025-06-01 17:30:07 UTC  
> Updated at: 2025-06-01 17:52:59 UTC  
> Merged at: 2025-06-01 17:49:51 UTC  
> Closed at: 2025-06-01 17:49:51 UTC  
> Url: https://github.com/boostorg/openmethod/pull/26  

* Deferred RTTI stored function pointers in `type_id`s, that was UB.   
* Do not assume that `std::uninptr_t` is available.  
* `type_id`s are now `const void*`.

---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-06-01 17:32:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/26#pullrequestreview-2885870844  

## Pull Request Overview  
  
This PR refactors custom RTTI to use `const void*` for `type_id`, eliminating undefined behavior from function-pointer casts and adding support for deferred static RTTI.  
  
- Change `type_id` alias from integer to pointer, updating all rtti policies and examples  
- Introduce a `deferred_static_rtti` flag and replace numeric sentinels with pointer-based logic  
- Remove old `type_id_list` machinery in favor of `init_type_ids` and `use_class_aux`  
  
### Reviewed Changes  
  
Copilot reviewed 12 out of 12 changed files in this pull request and generated 3 comments.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                            | Description                                                    |  
| ----------------------------------------------- | -------------------------------------------------------------- |  
| test/test_custom_rtti.cpp                       | Update tests to use `nullptr` and pointer-based `type_id`      |  
| test/test_core.cpp                              | Rename and rewrite test to use `init_type_ids`                 |  
| include/boost/openmethod/registry.hpp           | Add `deferred_static_rtti` flag                                |  
| include/boost/openmethod/policies/vptr_vector.hpp | Cast `type_id` to `size_t` for indexing                       |  
| include/boost/openmethod/policies/std_rtti.hpp   | Return `&typeid(...)` directly                                  |  
| include/boost/openmethod/policies/fast_perfect_hash.hpp | Change `hash_mult` and `hash()` to use `std::size_t`     |  
| include/boost/openmethod/detail/types.hpp        | Change `type_id` to `const void*`, define `uintptr` alias      |  
| include/boost/openmethod/core.hpp                | Remove old `type_id_list`, add `init_type_ids` & `use_class_aux` |  
| examples/deferred_custom_rtti.cpp                | Update example for pointer-based `type_id`                     |  
| examples/custom_rtti.cpp                         | Update example for pointer-based `type_id`                     |  
| CMakeLists.txt                                   | Add MSVC `/bigobj` compile option                              |  
</details>

📁 test/test_custom_rtti.cpp

```diff
 465 |             static const char* name[] = {"Animal", "Dog", "Cat"};
 469 |-             stream << (type == 0 ? "?" : name[type]);
 466 |+             stream << (type == 0 ? "?" : name[std::size_t(type)]);
```

> Username: Copilot  
> Created_at: 2025-06-01 17:32:14 UTC  
> Updated_at: 2025-06-01 17:32:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/26#discussion_r2119367914  

[nitpick] Use nullptr instead of integer literal 0 when comparing `type_id` pointers for consistency and clarity.  
```diff  
-            stream << (type == 0 ? "?" : name[std::size_t(type)]);  
+            stream << (type == nullptr ? "?" : name[std::size_t(type)]);  
```

---

📁 test/test_custom_rtti.cpp

```diff
 149 |             } else {
 151 |-                 return invalid_type;
 150 |+                 return type_id(0xFFFFFF);
```

> Username: Copilot  
> Created_at: 2025-06-01 17:32:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/26#discussion_r2119367915  

[nitpick] Avoid magic literal `0xFFFFFF`; extract this sentinel into a named constant or enum to clarify its purpose and ensure consistency.  
```diff  
-                return type_id(0xFFFFFF);  
+                return type_id(UNKNOWN_TYPE_ID);  
```


📁 include/boost/openmethod/detail/types.hpp

```diff
 131 | };
 132 | 
 133 |+ struct defered_class_info : class_info {
```

> Username: Copilot  
> Created_at: 2025-06-01 17:32:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/26#discussion_r2119367916  

Typo in struct name `defered_class_info`; it should be `deferred_class_info` to match the correct spelling and other `deferred_*` types.  
```diff  
-struct defered_class_info : class_info {  
+struct deferred_class_info : class_info {  
```


---
