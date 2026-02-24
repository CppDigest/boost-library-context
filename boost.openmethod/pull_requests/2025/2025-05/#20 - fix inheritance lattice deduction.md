# #20 fix inheritance lattice deduction [Merged]

> Username: jll63  
> Created at: 2025-05-12 21:03:53 UTC  
> Updated at: 2025-05-14 00:35:32 UTC  
> Merged at: 2025-05-14 00:30:17 UTC  
> Closed at: 2025-05-14 00:30:17 UTC  
> Url: https://github.com/boostorg/openmethod/pull/20  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-05-12 21:04:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/20#pullrequestreview-2834555018  

## Pull Request Overview  
  
This PR fixes issues with inheritance lattice deduction by updating class augmentation logic and adding a new recursive method to collect transitive bases. Key changes include the addition of collect_transitive_bases(), revising the base-collection loop in augment_classes(), and a minor correction to an error message for an unknown class.

📁 include/boost/openmethod/compiler.hpp

```diff
 357 |+ void compiler<Policy>::collect_transitive_bases(
 358 |+     class_* cls, class_* base) {
 359 |+         cls->transitive_bases.push_back(base);
```

> Username: Copilot  
> Created_at: 2025-05-12 21:04:47 UTC  
> Updated_at: 2025-05-12 21:04:48 UTC  
> Url: https://github.com/boostorg/openmethod/pull/20#discussion_r2085495613  

Consider checking whether the base is already present in cls->transitive_bases before pushing it, to prevent potential duplicates in the transitive bases list.  
```diff  
-        cls->transitive_bases.push_back(base);  
+        if (std::find(cls->transitive_bases.begin(), cls->transitive_bases.end(), base) == cls->transitive_bases.end()) {  
+            cls->transitive_bases.push_back(base);  
+        }  
```

---

📁 include/boost/openmethod/compiler.hpp

```diff
 408 |     for (auto& cr : Policy::classes) {
 398 |-         auto& rtc = class_map[Policy::type_index(cr.type)];
 409 |+         auto rtc = class_map[Policy::type_index(cr.type)];
```

> Username: Copilot  
> Created_at: 2025-05-12 21:04:48 UTC  
> Url: https://github.com/boostorg/openmethod/pull/20#discussion_r2085495622  

[nitpick] If rtc is intended to reference an object whose modifications should update the underlying data in class_map, consider using a reference (auto&) instead of copying the pointer.  
```diff  
+        auto& rtc = class_map[Policy::type_index(cr.type)];  
```

---

📁 include/boost/openmethod/compiler.hpp

```diff
 546 |-                 ++trace << "unkown class " << ti << "(" << type_name(ti)
 556 |+                 ++trace << "unknown class " << ti << "(" << type_name(ti)
 557 |                         << ") for parameter #" << (param_index + 1) << "\n";
```

> Username: Copilot  
> Created_at: 2025-05-12 21:04:48 UTC  
> Url: https://github.com/boostorg/openmethod/pull/20#discussion_r2085495627  

The typo 'unkown' has been corrected to 'unknown' in the error message. Verify that the corrected wording aligns with the project's error messaging standards.  
```diff  
+                ++trace << "Error: Unknown class " << ti << " (" << type_name(ti)  
+                        << ") for parameter #" << (param_index + 1) << ".\n";  
```


---

## Review 2 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-05-14 00:28:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/20#pullrequestreview-2838409086  

## Pull Request Overview  
  
This PR addresses fixes in the inheritance lattice deduction by updating the transitive base collection and sorting logic, as well as by enhancing the test coverage for linear inheritance.    
- Renamed and added a new test case (test_use_classes_linear) to specifically test linear inheritance relationships.    
- Modified the compiler implementation by replacing weight-based sorting with a sort based on the number of transitive bases, and fixed a spelling mistake in an error message.  
  
### Reviewed Changes  
  
Copilot reviewed 4 out of 4 changed files in this pull request and generated 2 comments.  
  
| File                                      | Description                                                                                   |  
| ----------------------------------------- | --------------------------------------------------------------------------------------------- |  
| test/test_compiler.cpp                    | Added a new linear inheritance test case and updated test names to better reflect behavior.   |  
| include/boost/openmethod/compiler.hpp     | Revised transitive base collection, updated sorting logic, and fixed a typo in an error message. |  
  
  
<details>  
<summary>Comments suppressed due to low confidence (1)</summary>  
  
**test/test_compiler.cpp:96**  
* [nitpick] Consider giving the test case a more descriptive name that clearly reflects it targets linear inheritance scenarios, making its intent immediately clear to reviewers.  
```  
BOOST_AUTO_TEST_CASE(test_use_classes_linear) {  
```  
</details>

📁 include/boost/openmethod/compiler.hpp

```diff
 409 |     for (auto& cr : Policy::classes) {
 398 |-         auto& rtc = class_map[Policy::type_index(cr.type)];
 410 |+         auto rtc = class_map[Policy::type_index(cr.type)];
```

> Username: Copilot  
> Created_at: 2025-05-14 00:28:45 UTC  
> Updated_at: 2025-05-14 00:28:46 UTC  
> Url: https://github.com/boostorg/openmethod/pull/20#discussion_r2087827979  

[nitpick] If class_map returns a pointer, this use of auto is acceptable; however, if reference semantics were intended, consider using 'auto&' to avoid any unintended copies.  
```diff  
-        auto rtc = class_map[Policy::type_index(cr.type)];  
+        auto& rtc = class_map[Policy::type_index(cr.type)];  
```

---

📁 include/boost/openmethod/compiler.hpp

```diff
 556 |             if (!class_) {
 546 |-                 ++trace << "unkown class " << ti << "(" << type_name(ti)
 557 |+                 ++trace << "unknown class " << ti << "(" << type_name(ti)
```

> Username: Copilot  
> Created_at: 2025-05-14 00:28:46 UTC  
> Url: https://github.com/boostorg/openmethod/pull/20#discussion_r2087827984  

Corrected the spelling of 'unknown' from the previously misspelled version in the error message.


---
