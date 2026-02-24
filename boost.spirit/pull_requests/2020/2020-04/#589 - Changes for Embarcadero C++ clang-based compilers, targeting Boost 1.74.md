# #589 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Closed]

> Username: eldiener  
> Created at: 2020-04-19 00:56:26 UTC  
> Updated at: 2020-05-06 20:14:09 UTC  
> Closed at: 2020-05-06 20:14:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/589  



---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-04-19 12:57:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/589#issuecomment-616128255  

Is the old Borland compiler available for you? Maybe we simply can get rid of these workarounds if they are not already enough to make things working on those outdated compilers.

---

## Comment 2

> Username: eldiener  
> Created_at: 2020-04-20 01:03:21 UTC  
> Updated_at: 2020-04-20 01:04:01 UTC  
> Url: https://github.com/boostorg/spirit/pull/589#issuecomment-616257353  

I only have the latest C++ Builder Community Edition 10.3. I could try to fish out from my old discs ancient editions of C++ Builder but I really do not want to bother. I could also try building with the current bcc32 compiler, but I really have little hope that it can compile current Spirit or most any other Boost library without lots of problems, and I do not want to waste any of my time trying getting bcc32 to work. It is enough work getting the Embarcadero C++ clang-based compilers to work with Boost libaries but at least there I have a good chance. As for getting rid of the workarounds for bcc32, that is purely up to individual maintainers. My PR should not affect any of those workarounds in any way.

---

## Review 3 [Commented]

> Username: Kojoley  
> Created_at: 2020-05-03 19:20:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/589#pullrequestreview-404649163  

📁 include/boost/spirit/home/lex/lexer/lexertl/functor_data.hpp

```diff
  70 | // needed leading to false static asserts
  71 |- #if !defined(__GNUC__)
  71 |+ #if !defined(__GNUC__) && !defined(BOOST_EMBTC)
```

> Username: Kojoley  
> Created_at: 2020-05-03 19:20:50 UTC  
> Updated_at: 2020-05-06 20:14:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/589#discussion_r419147280  

I do not understand why this is needed, both `clang++` and `clang-cl` does not fire an assertion in  
```cpp  
template <typename T>  
void foo(T)  
{  
  static_assert(false, "");  
}  
```  
However the code itself is ill-formed and needs a rewrite...

> Username: eldiener  
> Created_at: 2020-05-03 19:58:29 UTC  
> Updated_at: 2020-05-06 20:14:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/589#discussion_r419152001  

I was just being careful because clang on Linux and clang on Windows targeting gcc both define \_\_GNUC\_\_ and the Embarcadero C++ clang-based compilers define \_\_clang\_\_ but do not define \_\_GNUC\_\_. However I can remove this case as long as I know it does not also refer to clang. I hope you do realize that \_\_GNUC\_\_ does refer to clang also in the vast majority of cases like in the above code, unless you separately check that \_\_clang\_\_ is or is not also defined also.

> Username: eldiener  
> Created_at: 2020-05-05 00:30:57 UTC  
> Updated_at: 2020-05-06 20:14:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/589#discussion_r419805280  

I picked up the latest 'develop' changes and merged them in my PR.


---

## Comment 4

> Username: eldiener  
> Created_at: 2020-05-06 17:38:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/589#issuecomment-624789572  

Can we please merge this PR ?

---
