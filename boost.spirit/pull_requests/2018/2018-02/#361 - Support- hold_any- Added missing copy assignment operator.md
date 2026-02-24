# #361 Support: hold_any: Added missing copy assignment operator [Merged]

> Username: Kojoley  
> Created at: 2018-02-18 00:11:34 UTC  
> Updated at: 2018-02-18 19:10:31 UTC  
> Merged at: 2018-02-18 17:36:46 UTC  
> Closed at: 2018-02-18 17:36:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/361  

The PR #36 does not fixed problem for C++03 and wide range of MSVC compilers.  
  
Closes [trac 8268](https://svn.boost.org/trac10/ticket/8268).

---

## Comment 1

> Username: hkaiser  
> Created_at: 2018-02-18 18:39:52 UTC  
> Updated_at: 2018-02-18 18:40:17 UTC  
> Url: https://github.com/boostorg/spirit/pull/361#issuecomment-366537067  

Why was this necessary? The existing `operator=()`s (for both, `BOOST_HAS_RVALUE_REFS` being defined and not) were perfectly fine, iiuc.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-02-18 18:55:22 UTC  
> Updated_at: 2018-02-18 18:55:45 UTC  
> Url: https://github.com/boostorg/spirit/pull/361#issuecomment-366538106  

I failed to understand from the C++ standard when used defined templated assignment operator suppresses compiler generated copy assignment operator. From what I have tried:  
  - C++11: `T&&` takes precedence, but not for older MSVC (I did not tried GCC and Clang may be they have the problem too on older versions).  
  - C++03: `T const&` does not in every compiler.  
  
The simplest workaround for all the cases is just add copy assignment operator.

---

## Comment 3

> Username: hkaiser  
> Created_at: 2018-02-18 19:10:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/361#issuecomment-366539256  

@Kojoley ok, fair enough. In this case I'd suggest to add an explicit `operator=(any&&)` as well (if `BOOST_HAS_RVALUE_REFS` is defined).

---
