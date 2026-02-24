# #30 - Implement __VA_OPT__ for C++20 [Closed]

> Username: hkaiser  
> Created at: 2018-01-01 12:53:16 UTC  
> Updated at: 2020-06-23 19:05:19 UTC  
> Closed at: 2020-06-23 19:05:19 UTC  
> Url: https://github.com/boostorg/wave/issues/30  

C++20 will introduce `__VA_OPT__ (see: http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2017/p0306r4.html), we should implement it in wave.  
  
This shouldn't be difficult to implement as it's functionality is similar to `__VA_ARGS__`. The semantics are as follows: If the variable arguments contain no tokens, then `__VA_OPT__(content)` is replaced by no tokens (more precisely, by a placemarker). Otherwise, it is replaced by `content`, which can contain any admissible replacement text, including `__VA_ARGS__`.  
  
As a side effect we'll need to introduce a new `language_support` option ` support_cpp2z` and a new configuration option `BOOST_WAVE_SUPPORT_CPP2Z`.

---

## Comment 1

> Username: mattcalabrese  
> Created at: 2018-01-30 20:44:01 UTC  
> Url: https://github.com/boostorg/wave/issues/30#issuecomment-361728047  

Recommend calling it 2A instead of 2Z (gcc and clang options for C++20 refer to it as c++2a).

---

## Comment 2

> Username: hkaiser  
> Created at: 2018-01-30 21:19:52 UTC  
> Url: https://github.com/boostorg/wave/issues/30#issuecomment-361737753  

@mattcalabrese Good point, thanks!

---

## Comment 3

> Username: eldiener  
> Created at: 2019-10-25 10:42:34 UTC  
> Url: https://github.com/boostorg/wave/issues/30#issuecomment-546303050  

Has this ever been implemented in Wave ? I am updating the Boost PP library with some support for detecting the __VA_OPT__ construct and a ffolproof test for emptiness using __VA_OPT__, and it would be nice to use the Wave driver, as I have so often done, to see what the expansion of macros using __VA_OPT__ should be like. I think this also needs a new Wave driver command line option, maybe something like '--c++20'. As always I am very appreciative of Wave for testing correct macro expansion.

---

## Comment 4

> Username: hkaiser  
> Created at: 2019-10-25 11:23:03 UTC  
> Url: https://github.com/boostorg/wave/issues/30#issuecomment-546315415  

This has not been implemented in Wave yet, sorry.

---

## Comment 5

> Username: jefftrull  
> Created at: 2020-02-28 04:59:44 UTC  
> Url: https://github.com/boostorg/wave/issues/30#issuecomment-592314226  

@eldiener have a look at [my branch](https://github.com/jefftrull/wave/tree/feature/va-opt) - would love to have some feedback

---

## Comment 6

> Username: jefftrull  
> Created at: 2020-03-15 21:02:44 UTC  
> Url: https://github.com/boostorg/wave/issues/30#issuecomment-599264252  

I believe this is now fixed in develop but will keep it open in case others want to test or have other feedback prior to 1.74.

---

## Comment 7

> Username: jefftrull  
> Created at: 2020-06-23 19:05:19 UTC  
> Url: https://github.com/boostorg/wave/issues/30#issuecomment-648358559  

This has gone into master for 1.74.
