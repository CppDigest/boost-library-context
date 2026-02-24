# #830 Modernize numeric [Closed]

> Username: saki7  
> Created at: 2025-09-11 16:09:21 UTC  
> Updated at: 2025-09-13 03:32:56 UTC  
> Closed at: 2025-09-13 01:33:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/830  

Part of boostorg/spirit_x4#4   
  
This PR modernizes `numeric` and `numeric_utils` components.  
  
- Add `constexpr` to almost everywhere  
- Use `if constexpr` in static branches  
- Use iterator/sentinel pair, avoid unconstrained `typename Iterator`  
- Add `noexcept` to primitive operations  
- Use `<cstdint>` instead of Boost version  
- Use `<type_traits>` instead of Boost version

---

## Comment 1

> Username: saki7  
> Created_at: 2025-09-13 01:33:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/830#issuecomment-3287349795  

Superseded by boostorg/spirit_x4#17

---
