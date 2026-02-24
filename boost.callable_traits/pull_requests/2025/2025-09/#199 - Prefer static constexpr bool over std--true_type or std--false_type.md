# #199 Prefer static constexpr bool over std::true_type or std::false_type [Open]

> Username: jonesmz  
> Created at: 2025-09-25 21:23:19 UTC  
> Updated at: 2025-09-25 22:00:06 UTC  
> Url: https://github.com/boostorg/callable_traits/pull/199  

Over using std::true_type or std::false_type.  
  
While the std::integral_constant derivatives aren't particularly heavy weight, they still add up in a codebase that sees them used in many places. Directly using static constexpr bool variables should reduce the number of template instantiations required, reducing build times.

---
