# #2 statically check that a class is polymorphic (where needed) [Merged]

> Username: jll63  
> Created at: 2025-04-16 23:21:59 UTC  
> Updated at: 2025-04-17 22:12:38 UTC  
> Merged at: 2025-04-17 22:10:25 UTC  
> Closed at: 2025-04-17 22:10:25 UTC  
> Url: https://github.com/boostorg/openmethod/pull/2  

* Restore static checks for non-polymorphic classes.  
* Fix error handler in the debug policy.

---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-04-16 23:22:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/2#pullrequestreview-2774105579  

## Pull Request Overview  
  
This PR adds static compile-time checks for polymorphism in various RTTI policies by introducing and using the is_polymorphic template variable. Key changes include:  
- Adding a static constexpr is_polymorphic flag to custom_rtti implementations in tests, examples, and policies.  
- Refactoring if constexpr checks to use is_polymorphic in static_type and dynamic_type functions.  
- Updating dynamic_type template constraints in std_rtti using SFINAE.  
  
### Reviewed Changes  
  
Copilot reviewed 6 out of 9 changed files in this pull request and generated no comments.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                                        | Description                                                            |  
| ----------------------------------------------------------- | ---------------------------------------------------------------------- |  
| test/test_custom_rtti.cpp                                   | Updated custom_rtti implementations to use is_polymorphic and different fallback values. |  
| include/boost/openmethod/policies/std_rtti.hpp              | Added is_polymorphic flag based on std::is_polymorphic_v and refined dynamic_type.         |  
| include/boost/openmethod/policies/minimal_rtti.hpp          | Defined is_polymorphic as false for minimal_rtti.                       |  
| include/boost/openmethod/policies.hpp                       | Adjusted policy chaining to include a replacement for error_handler.   |  
| include/boost/openmethod/core.hpp                           | Extended runtime_checks to also require polymorphic types in virtual_ptr and modified error handling. |  
| examples/custom_rtti.cpp                                    | Mirrored changes in custom_rtti to leverage is_polymorphic.            |  
</details>  
  
  
<details>  
<summary>Files not reviewed (3)</summary>  
  
* **doc/custom_rtti.adoc**: Language not supported  
* **doc/minimal_rtti.adoc**: Language not supported  
* **doc/rtti.adoc**: Language not supported  
</details>  
  
<details>  
<summary>Comments suppressed due to low confidence (2)</summary>  
  
**test/test_custom_rtti.cpp:141**  
* The non-polymorphic branch in static_type returns 666 in this block, while another block returns 0. Consider unifying the fallback values to avoid potential inconsistencies.  
```  
return 666;  
```  
**test/test_custom_rtti.cpp:48**  
* The static_type function in one custom_rtti block applies a reinterpret_cast while others do not. Consider using a consistent approach for returning static_type values across implementations.  
```  
return reinterpret_cast<type_id>(T::static_type);  
```  
</details>

---

## Review 2 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-04-16 23:26:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/2#pullrequestreview-2774109774  

## Pull Request Overview  
  
This PR aims to statically verify if a class is polymorphic by introducing an is_polymorphic template constant across various RTTI policy implementations.    
- Introduces a template constant is_polymorphic to replace direct use of std::is_base_of_v<Animal, T> in several custom RTTI implementations.    
- Updates different policy files and examples to conditionally select between static and dynamic type functions.    
- Modifies error handling code in core.hpp to leverage the new is_polymorphic check.  
  
### Reviewed Changes  
  
Copilot reviewed 6 out of 9 changed files in this pull request and generated no comments.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File | Description |  
| ---- | ----------- |  
| test/test_custom_rtti.cpp | Replaces direct polymorphism checks with is_polymorphic in custom RTTI structures. |  
| include/boost/openmethod/policies/std_rtti.hpp | Introduces is_polymorphic based on std::is_polymorphic_v. |  
| include/boost/openmethod/policies/minimal_rtti.hpp | Sets is_polymorphic to false for minimal RTTI. |  
| include/boost/openmethod/policies.hpp | Adjusts policy chaining with a replacement of the error handler. |  
| include/boost/openmethod/core.hpp | Adds polymorphic checks into runtime validations and error handling. |  
| examples/custom_rtti.cpp | Updates example to use the newly added is_polymorphic check. |  
</details>  
  
  
<details>  
<summary>Files not reviewed (3)</summary>  
  
* **doc/custom_rtti.adoc**: Language not supported  
* **doc/minimal_rtti.adoc**: Language not supported  
* **doc/rtti.adoc**: Language not supported  
</details>  
  
<details>  
<summary>Comments suppressed due to low confidence (2)</summary>  
  
**test/test_custom_rtti.cpp:47**  
* There is an inconsistency in the fallback return values across custom_rtti implementations (some returning 0 and others 666). Please verify if the default value should be standardized to avoid potential logic errors.  
```  
if constexpr (is_polymorphic<T>) {  
```  
**include/boost/openmethod/core.hpp:1302**  
* Using static_type<void>() as a fallback in error_type_id may be questionable if static_type is not defined for void. Please confirm that invoking static_type with void is valid in this context.  
```  
return Policy::template static_type<void>();  
```  
</details>

---
