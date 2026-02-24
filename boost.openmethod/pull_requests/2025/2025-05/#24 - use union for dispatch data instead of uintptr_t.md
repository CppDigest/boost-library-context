# #24 use union for dispatch data instead of uintptr_t [Merged]

> Username: jll63  
> Created at: 2025-05-26 19:24:23 UTC  
> Updated at: 2025-05-26 19:35:09 UTC  
> Merged at: 2025-05-26 19:32:37 UTC  
> Closed at: 2025-05-26 19:32:37 UTC  
> Url: https://github.com/boostorg/openmethod/pull/24  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-05-26 19:32:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/24#pullrequestreview-2869075850  

## Pull Request Overview  
  
This PR refactors the dispatch data handling by replacing the use of std::uintptr_t with a union type (detail::word) for more precise representation of pointers and integers. Key changes include updating data types across the registry, core, and compiler modules, and adjusting the associated reinterpret_casts for function pointers.  
- Changed the dispatch_data type in the registry.  
- Updated function return types and pointer casts in core and compiler modules.  
- Introduced a union (detail::word) in types.hpp to encapsulate different representations.  
  
### Reviewed Changes  
  
Copilot reviewed 6 out of 6 changed files in this pull request and generated no comments.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                                     | Description                                            |  
| -------------------------------------------------------- | ------------------------------------------------------ |  
| include/boost/openmethod/registry.hpp                    | Updated dispatch_data to use detail::word              |  
| include/boost/openmethod/macros.hpp                     | Modified has_next implementation                     |  
| include/boost/openmethod/detail/types.hpp               | Introduced union word and updated related type aliases |  
| include/boost/openmethod/detail/ostdstream.hpp           | Added overload for operator<< with function pointer    |  
| include/boost/openmethod/core.hpp                        | Updated resolve functions to use detail::word          |  
| include/boost/openmethod/compiler.hpp                    | Changes to pointer storage in compiler data structures  |  
</details>

---
