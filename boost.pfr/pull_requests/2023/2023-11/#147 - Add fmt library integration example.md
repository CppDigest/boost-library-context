# #147 Add fmt library integration example [Open]

> Username: denzor200  
> Created at: 2023-11-25 22:33:54 UTC  
> Updated at: 2023-12-03 14:27:25 UTC  
> Url: https://github.com/boostorg/pfr/pull/147  



---

## Review 1 [Commented]

> Username: denzor200  
> Created_at: 2023-12-03 14:27:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/147#pullrequestreview-1761251039  

📁 example/fmtlib_formatter.cpp

```diff
   8 |+ #include <type_traits>
   9 |+ 
  10 |+ namespace boost::pfr {
```

> Username: denzor200  
> Created_at: 2023-12-03 14:26:30 UTC  
> Updated_at: 2023-12-03 14:27:25 UTC  
> Url: https://github.com/boostorg/pfr/pull/147#discussion_r1413112972  

`namespace boost::pfr::extension {` ??

---

📁 example/fmtlib_formatter.cpp

```diff
  60 |+ };
  61 |+ 
  62 |+ template<> struct boost::pfr::is_reflectable<point, boost::pfr::fmtlib_tag> : std::integral_constant<bool, true> {};
```

> Username: denzor200  
> Created_at: 2023-12-03 14:27:16 UTC  
> Updated_at: 2023-12-03 14:27:25 UTC  
> Url: https://github.com/boostorg/pfr/pull/147#discussion_r1413113096  

`template<typename P> struct boost::pfr::is_reflectable<point, P> : std::integral_constant<bool, true> {};`  
Same for another specializations


---
