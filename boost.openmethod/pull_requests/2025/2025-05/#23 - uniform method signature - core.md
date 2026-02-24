# #23 uniform method signature - core [Merged]

> Username: jll63  
> Created at: 2025-05-25 20:49:18 UTC  
> Updated at: 2025-06-21 18:21:42 UTC  
> Merged at: 2025-05-25 21:11:20 UTC  
> Closed at: 2025-05-25 21:11:20 UTC  
> Url: https://github.com/boostorg/openmethod/pull/23  

Related to https://github.com/jll63/Boost.OpenMethod/issues/10  
  
Make `method` use the actual function type as its second parameter. I.e. what used to read    
`method<Name(Parameters...), ReturnType, Registry>` is now `method<Name, auto(Parameters...)->ReturnType, Registry>` (or `method<Name, ReturnType(Parameters...), Registry>`). This aligns better with other constructs which work in terms of method-id then method-signature. It also better corresponds to the method-id's goal, which is to discriminate between different methods with the same signature.  
  
As for switching the macros to `BOOST_OPENMETHOD(id, (Args&&... args)->ReturnType)`, the idea is still being debated.

---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-05-25 20:50:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/23#pullrequestreview-2866939970  

## Pull Request Overview  
  
This PR updates the method signature syntax to use the actual function type (using the auto(Parameters…)->ReturnType pattern) rather than the old function‐style syntax. Key changes include updating method invocations in various test files and examples, and revising internal macros and the core header to reflect the uniform signature.  
  
### Reviewed Changes  
  
Copilot reviewed 9 out of 9 changed files in this pull request and generated 1 comment.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                        | Description                                                    |  
| ------------------------------------------- | -------------------------------------------------------------- |  
| test/test_virtual_ptr_value_semantics.hpp   | Updates method signature invocation using new auto() syntax.   |  
| test/test_virtual_ptr_dispatch.cpp          | Updates several method invocations; note potential signature loss in one fight definition. |  
| test/test_member_method.cpp                   | Adjusted member method signature to new syntax.                |  
| test/test_compiler.cpp                         | Updated macro usages to the uniform method signature.          |  
| test/test_blackbox.cpp                         | Revised method invocations to use the new signature pattern.     |  
| include/boost/openmethod/macros.hpp            | Adjusted macro definitions to extract return type and registry.  |  
| include/boost/openmethod/core.hpp              | Changes method template specialization to adopt new signature.   |  
| examples/slides.cpp                           | Updated example usage of method with auto() signature.           |  
| examples/core_api.cpp                         | Revised example method definitions; note potential omission of Registry parameter in some instances. |  
</details>  
  
  
  
<details>  
<summary>Comments suppressed due to low confidence (2)</summary>  
  
**examples/core_api.cpp:71**  
* [nitpick] Confirm that omitting the Registry parameter here is intentional and that a default is provided, to maintain consistency with its usage in other parts of the codebase.  
```  
using pet = method<BOOST_OPENMETHOD_NAME(pet), auto(std::ostream&, virtual_ptr<Animal>)->void>;  
```  
**test/test_virtual_ptr_dispatch.cpp:284**  
* The first definition of the 'fight' method appears to only specify one parameter (virtual_ptr<Player, Registry>) instead of three as seen in the later usage. Please verify if this reduction is intentional or if additional parameters should be included.  
```  
BOOST_OPENMETHOD_NAME(fight),  
```  
</details>

📁 include/boost/openmethod/core.hpp

```diff
 966 |     typename Name, typename... Parameters, typename ReturnType, class Registry>
 965 |- class method<Name(Parameters...), ReturnType, Registry>
 967 |+ class method<Name, auto(Parameters...)->ReturnType, Registry>
```

> Username: Copilot  
> Created_at: 2025-05-25 20:50:08 UTC  
> Url: https://github.com/boostorg/openmethod/pull/23#discussion_r2106303724  

[nitpick] It would be helpful to update the documentation/comments near the method template specialization to explain the new auto(Parameters...)->ReturnType syntax and its intended usage, to aid future maintainers.


---
