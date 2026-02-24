# #906 - Fixed-width floating-point C++23 and <cstdfloat.hpp> [Closed]

> Username: ckormanyos  
> Created at: 2022-12-28 09:53:22 UTC  
> Updated at: 2023-06-28 06:02:27 UTC  
> Closed at: 2023-06-28 06:02:27 UTC  
> Url: https://github.com/boostorg/math/issues/906  

Hi John (@jzmaddock) and Matt (@mborland)  
  
I've been pleasantly looking at [fixed-width floating-point](https://en.cppreference.com/w/cpp/types/floating-point) types for C++23. There was a recent update of an original idea from the past, which seems to have gotten enough traction to get into the lang.  
  
Upon inspection of the wording, the link states  
- "... the fixed width floating-point types must be aliases to extended floating point types (not `float` / `double` / `long` `double`)."  
  
In light of this, does it make sense (for those compilers supporting this feature) to change the detection mechanism for a compiler's _having_ `boost::float32_t`, `boost::float64_t`, etc.? We used the presence of built-in types with the relevant digit counts for detection previously.  
  
I wonder if this proposal means for C++23 and beyond that it's better to check for the presence of the extended types? I suppose on a practical level, it makes no difference whatsoever, but it would be formally correct under-the-hood?  
  
Thoughts?

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-12-28 10:06:57 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1366525776  

I guess I'd wonder what (in C++23) should be the result of  
  
?  
  
```  
// What's up here if C++ compiler supports C++23 float32_t?  
std::assert(std::is_same<boost::float32_t, float>::value);  
```

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-12-28 11:09:27 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1366570698  

There's maybe other questoins that'll arise like:  
- Interact how with `<limits>` / `<complex>`?  
- Do templates need to specifically target the range of `std::float16_t` now that it may become more prevalent?  
  
And more stuff that might arise. Similar (but different) questions might arise for Multirpecision.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2022-12-28 11:26:34 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1366581304  

I looked a little deeper, and [this checkin](https://github.com/gcc-mirror/gcc/commit/a23225fb4f764dfc3e3e729c7d7238f03f282aaa) is cool with provisions for `<limits>`, atomics, `<type_traits>`, `std::abs()`, etc.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-12-28 13:32:20 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1366656541  

I see two issue here:  
  
* We should probably do what the std says (BTW I couldn't find any mention of <cstdfloat> in the working draft??) and use the C-language extension types in our cstdfloat.hpp when available (if we can tell).  
* We should start testing our code against _Float32 etc as well as float/double etc.  Mostly just some concept checks would do here I hope, we don't really want to have to double the testing load :(

---

## Comment 5

> Username: ckormanyos  
> Created at: 2022-12-28 13:35:33 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1366658393  

That seems reasonable.  
  
As for 16-bit, I don't have the strength at the moment to address the issue of `float16_t` and `<cmath>`/`<complex>`, neither in Boost nor with the committee in the std-draft.

---

## Comment 6

> Username: ckormanyos  
> Created at: 2022-12-28 13:36:29 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1366658904  

Should I close this issue John (@jzmaddock), or leave it hanging around for the future?

---

## Comment 7

> Username: jzmaddock  
> Created at: 2022-12-28 18:20:52 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1366837736  

float16_t is basically a storage type only, I can't imagine doing [complex] arithmetic with it.  We should just update the logic in promote.hpp to promote it to "whatever" C++ does: float?  Not sure if it's even specified?  
  
Maybe leave this hanging around as a reminder for now?

---

## Comment 8

> Username: mborland  
> Created at: 2022-12-31 11:30:21 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1368203595  

>   
  
[Here is the section on conversion rank and promotion](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p1467r9.html#rank). Going by the typical rhythm for GCC late April is when we should see GCC 13 with support for these new types.

---

## Comment 9

> Username: mborland  
> Created at: 2023-04-24 11:14:00 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1519939765  

GCC-13 is now available on Ubuntu 23.04 and Fedora 38. This week I am going to investigate how these new types affect things.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2023-04-24 11:22:12 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1519951924  

Thanks Matt, does this mean we can get gcc-13.1 CI working?  If so enabling it for Boost.Config, and maybe setting up some macros for the new types might be the first order of business (and quick to do - CI cycles really fast on Config).

---

## Comment 11

> Username: mborland  
> Created at: 2023-04-24 11:25:07 UTC  
> Url: https://github.com/boostorg/math/issues/906#issuecomment-1519956480  

> Thanks Matt, does this mean we can get gcc-13.1 CI working? If so enabling it for Boost.Config, and maybe setting up some macros for the new types might be the first order of business (and quick to do - CI cycles really fast on Config).  
  
Not yet. The package is only available on 23.04 right now, and the latest version GHA has is 22.04.
