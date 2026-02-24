# #241 Add noexcept specs to x3::variant and forward_ast [Merged]

> Username: dodheim  
> Created at: 2017-08-25 01:09:46 UTC  
> Updated at: 2017-08-25 23:34:26 UTC  
> Merged at: 2017-08-25 23:34:26 UTC  
> Closed at: 2017-08-25 23:34:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/241  

This adds `noexcept` specifications to X3's `variant` and `forward_ast`. Missing specs on the types' move constructors was showing up in my profiling and I decided to fix up the types in whole.  
  
I wasn't sure whether to use `noexcept` directly or `BOOST_NOEXCEPT`/`BOOST_NOEXCEPT_IF`; I couldn't find any precedent so opted for the latter.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-08-25 23:34:12 UTC  
> Url: https://github.com/boostorg/spirit/pull/241#issuecomment-325056250  

Wonderful!

---
