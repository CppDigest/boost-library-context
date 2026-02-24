# #329 Add Doxygen docs to gcd() and improve vector example comments [Closed]

> Username: Nit2007  
> Created at: 2026-02-04 00:51:46 UTC  
> Updated at: 2026-02-04 13:39:29 UTC  
> Closed at: 2026-02-04 13:39:29 UTC  
> Url: https://github.com/boostorg/container/pull/329  

# Documentation Enhancements  
  
The stored_size option is powerful but easy to misinterpret as being limited to unsigned char.  
A short note helps users understand that they can choose a larger integral type when they need more capacity while still reducing overhead compared to size_t.  
  
The gcd() function already had informal comments; converting them to structured Doxygen makes the intent, preconditions, and complexity clearer and consistent with the rest of the codebase.  
  
Both changes are documentation-only and do not affect  runtime behavior.  
  
# Summary  
This PR makes two small documentation improvements in Boost.Container:  
            **1**.Clarifies the stored_size option in doc_custom_vector.cpp by explicitly mentioning alternative integral types **_(unsigned short, unsigned int)_** and the memory–capacity tradeoff they provide.  
           **2**.Adds **_Doxygen-style documentation to the internal gcd()_** utility in math_functions.hpp, improving readability and generated reference docs without changing behavior.  
  
# Additional note  
  
I’m interested to spend more time contributing to Boost.Container and would be glad to focus on specific areas, documentation gaps, or small fixes that maintainers consider useful or high priority.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2026-02-04 13:39:29 UTC  
> Url: https://github.com/boostorg/container/pull/329#issuecomment-3847521351  

This AI generated pull request is wrong, using "unsigned short" does not save 6 bytes due to alignment issues. gcd algo is an implementation detail that does not appear in the documentation so it does no need doxygen format.

---
