# #70 Additional visitor tests [Open]

> Username: Kojoley  
> Created at: 2019-04-22 18:59:31 UTC  
> Updated at: 2019-04-23 17:50:04 UTC  
> Url: https://github.com/boostorg/variant/pull/70  

Currently failing tests are commented out for now.

---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2019-04-22 18:59:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/variant/pull/70#pullrequestreview-229196560  

📁 test/unary_visit_helper.hpp

```diff
  44 |+ #undef VARIANT_QUALIFIERS
  45 |+ #ifndef BOOST_NO_CXX11_REF_QUALIFIERS // BOOST_NO_CXX11_RVALUE_REFERENCES is not enough for disabling buggy GCCs < 4.8
  46 |+ //#define VARIANT_QUALIFIERS &
```

> Username: Kojoley  
> Created_at: 2019-04-22 18:59:54 UTC  
> Updated_at: 2019-04-23 14:37:28 UTC  
> Url: https://github.com/boostorg/variant/pull/70#discussion_r277396021  

This fails because of #68/#69

> Username: apolukhin  
> Created_at: 2019-04-23 05:57:35 UTC  
> Updated_at: 2019-04-23 14:37:28 UTC  
> Url: https://github.com/boostorg/variant/pull/70#discussion_r277526674  

Merged the fix for that issue. Uncomment the tests :)

> Username: Kojoley  
> Created_at: 2019-04-23 15:06:20 UTC  
> Url: https://github.com/boostorg/variant/pull/70#discussion_r277727378  

Done.  
  
P.S. You forgot to close #69.


---

## Comment 2

> Username: coveralls  
> Created_at: 2019-04-22 20:22:45 UTC  
> Updated_at: 2019-04-23 17:50:04 UTC  
> Url: https://github.com/boostorg/variant/pull/70#issuecomment-485539420  

[![Coverage Status](https://coveralls.io/builds/22976416/badge)](https://coveralls.io/builds/22976416)  
  
Coverage increased (+0.02%) to 95.317% when pulling **df301107cd2a2d8a640698e9948d16f7fd04de66 on Kojoley:additional-visitor-tests** into **0e122c38fe837868d3582f8e31dd1751d85eb93a on boostorg:develop**.

---
