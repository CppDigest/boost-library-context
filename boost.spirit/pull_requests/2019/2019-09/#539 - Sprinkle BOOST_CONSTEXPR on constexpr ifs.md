# #539 Sprinkle BOOST_CONSTEXPR on constexpr ifs. [Closed]

> Username: rubenvb  
> Created at: 2019-09-21 13:13:33 UTC  
> Updated at: 2019-09-21 14:59:41 UTC  
> Closed at: 2019-09-21 14:59:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/539  

These probably don't improve performance, but should inhibit any future compiler warnings about constant conditional expressions (outside of the already worked around MSVC's C4127).  
  
Part 2 of fixes for #537

---

## Review 1 [Changes requested]

> Username: Kojoley  
> Created_at: 2019-09-21 13:41:19 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/spirit/pull/539#pullrequestreview-291465759  

You cannot do this, `BOOST_CONSTEXPR` is not intended for such usage, it expands to `conxtexpr` even on c++0x https://www.boost.org/doc/libs/master/libs/config/doc/html/boost_config/boost_macro_reference.html

---

## Comment 2

> Username: rubenvb  
> Created_at: 2019-09-21 14:59:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/539#issuecomment-533804533  

Right. Sorry for the brainfart. Never mind about this then.

---
