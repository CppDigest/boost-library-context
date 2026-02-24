# #34 registry scoped boost_openmethod_vptr [Merged]

> Username: jll63  
> Created at: 2025-06-20 23:05:37 UTC  
> Updated at: 2025-06-21 12:44:05 UTC  
> Merged at: 2025-06-21 12:43:42 UTC  
> Closed at: 2025-06-21 12:43:42 UTC  
> Url: https://github.com/boostorg/openmethod/pull/34  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-06-20 23:07:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/34#pullrequestreview-2947403610  

## Pull Request Overview  
  
This PR refactors registry policy types by introducing `release_registry` and `debug_registry`, updates `boost_openmethod_vptr` signatures to accept a registry parameter, and adjusts related tests and examples to reflect these changes.  
  
- Rename `policies::release`/`policies::debug` to `release_registry`/`debug_registry` and update `default_registry` alias.  
- Change `boost_openmethod_vptr` to take a registry pointer parameter in core, headers, tests, and examples.  
- Add static assertions in `test_core.cpp` for `has_vptr_fn` with the new registry types.  
  
### Reviewed Changes  
  
Copilot reviewed 6 out of 6 changed files in this pull request and generated 4 comments.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                            | Description                                           |  
| ----------------------------------------------- | ----------------------------------------------------- |  
| test/test_virtual_ptr_value_semantics.hpp       | Update smart pointer static_asserts to use new registry names |  
| test/test_core.cpp                              | Add `has_vptr_fn` checks; introduce two `Animal` definitions |  
| include/boost/openmethod/with_vptr.hpp          | Change `boost_openmethod_vptr` friend signatures to include a `void*` parameter |  
| include/boost/openmethod/default_registry.hpp   | Rename policy structs to registry types and update `default_registry` |  
| include/boost/openmethod/core.hpp               | Update `has_vptr_fn` and `acquire_vptr` to expect registry parameter |  
| examples/virtual_.cpp                           | Update example `boost_openmethod_vptr` signature to include `void*` |  
</details>

📁 test/test_core.cpp

```diff
 319 |+ } // namespace TEST_NS
 320 |+ 
 321 |+ namespace TEST_NS {
```

> Username: Copilot  
> Created_at: 2025-06-20 23:07:46 UTC  
> Updated_at: 2025-06-20 23:07:48 UTC  
> Url: https://github.com/boostorg/openmethod/pull/34#discussion_r2159749855  

Redefining `Animal` in the same namespace `TEST_NS` will cause a redefinition error. Consider merging the tests for default and debug registries into a single struct or using separate namespaces.  
```diff  
-namespace TEST_NS {  
+namespace TEST_NS_DEBUG {  
```


📁 include/boost/openmethod/with_vptr.hpp

```diff
  78 | 
  79 |-     friend auto boost_openmethod_vptr(const Class& obj) -> vptr_type {
  79 |+     friend auto boost_openmethod_vptr(const Class& obj, void*) -> vptr_type {
```

> Username: Copilot  
> Created_at: 2025-06-20 23:07:47 UTC  
> Updated_at: 2025-06-20 23:07:48 UTC  
> Url: https://github.com/boostorg/openmethod/pull/34#discussion_r2159749858  

The second parameter is declared as `void*`, but `has_vptr_fn` expects a `Registry*`. Change the parameter type to `Registry* registry` to match the core detection logic.  
```diff  
-    friend auto boost_openmethod_vptr(const Class& obj, void*) -> vptr_type {  
+    friend auto boost_openmethod_vptr(const Class& obj, Registry*) -> vptr_type {  
```

---

📁 include/boost/openmethod/with_vptr.hpp

```diff
 152 |-     friend auto boost_openmethod_vptr(const Class& obj) -> vptr_type {
 153 |-         return boost_openmethod_vptr(static_cast<const Base1&>(obj));
 152 |+     friend auto boost_openmethod_vptr(const Class& obj, void* registry) -> vptr_type {
```

> Username: Copilot  
> Created_at: 2025-06-20 23:07:47 UTC  
> Updated_at: 2025-06-20 23:07:48 UTC  
> Url: https://github.com/boostorg/openmethod/pull/34#discussion_r2159749860  

Using `void* registry` here will not match the expected `Registry*` overload. Update the signature to accept `Registry* registry` so `has_vptr_fn` detects it correctly.  
```diff  
-    friend auto boost_openmethod_vptr(const Class& obj, void* registry) -> vptr_type {  
+    friend auto boost_openmethod_vptr(const Class& obj, Registry* registry) -> vptr_type {  
```


📁 examples/virtual_.cpp

```diff
  43 |     boost::openmethod::vptr_type vptr;
  44 |-     friend auto boost_openmethod_vptr(const Animal& a) {
  44 |+     friend auto boost_openmethod_vptr(const Animal& a, void*) {
```

> Username: Copilot  
> Created_at: 2025-06-20 23:07:47 UTC  
> Updated_at: 2025-06-20 23:07:48 UTC  
> Url: https://github.com/boostorg/openmethod/pull/34#discussion_r2159749863  

The example uses `void*` for the registry parameter, but it should use the actual registry pointer type (`default_registry*` or the appropriate registry) to align with `has_vptr_fn` and core implementation.  
```diff  
-    friend auto boost_openmethod_vptr(const Animal& a, void*) {  
+    friend auto boost_openmethod_vptr(const Animal& a, boost::openmethod::default_registry*) {  
```


---
