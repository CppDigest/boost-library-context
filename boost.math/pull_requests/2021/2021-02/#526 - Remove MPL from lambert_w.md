# #526 Remove MPL from lambert_w [Merged]

> Username: mborland  
> Created at: 2021-02-09 18:01:38 UTC  
> Updated at: 2021-02-14 11:25:02 UTC  
> Merged at: 2021-02-13 19:16:55 UTC  
> Closed at: 2021-02-13 19:16:55 UTC  
> Url: https://github.com/boostorg/math/pull/526  

Remove MPL, and make other C++11 changes (e.g. boost::integral_constant to std::integral_constant).

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-02-09 19:09:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/526#pullrequestreview-586902444  

📁 include/boost/math/special_functions/lambert_w.hpp

```diff
1220 | {
1229 |-   static const char* function = "boost::math::lambert_w0<%1%>"; // For error messages.
1221 |+   static std::string function = "boost::math::lambert_w0<%1%>"; // For error messages.
```

> Username: jzmaddock  
> Created_at: 2021-02-09 19:09:55 UTC  
> Updated_at: 2021-02-10 16:35:17 UTC  
> Url: https://github.com/boostorg/math/pull/526#discussion_r573158758  

What's the motivation here?  A string literal avoids the (tiny) runtime overhead of initializing a std::string which we don't use (as that type) anyway?

> Username: mborland  
> Created_at: 2021-02-09 19:22:36 UTC  
> Updated_at: 2021-02-10 16:35:17 UTC  
> Url: https://github.com/boostorg/math/pull/526#discussion_r573168522  

Mostly to remove non-smart pointers that are floating around as part of the modernization campaign. If there is a `BOOST_CXX20_CONSTEXPR` macro it could become `constexpr`.

> Username: jzmaddock  
> Created_at: 2021-02-09 19:30:02 UTC  
> Updated_at: 2021-02-10 16:35:17 UTC  
> Url: https://github.com/boostorg/math/pull/526#discussion_r573175296  

If the naked pointer bothers you (it might not be officially constexpr, but it is compile time initialized, and possibly fixed up at program load time) then how about:  
```  
static constexpr const function[] = { "foobar" };  
```  
My point was that creating a std::string was actually worse than the original as it involves memory allocation, plus a thread lock on every function entry to check whether the string has been constructed or not.  OK the lock may be just an atomic read, but still.

> Username: mborland  
> Created_at: 2021-02-10 16:35:49 UTC  
> Url: https://github.com/boostorg/math/pull/526#discussion_r573883491  

I reverted it to `const char*`; I would say that is more idiomatic than a C style array.


---

## Comment 2

> Username: NAThompson  
> Created_at: 2021-02-09 19:56:04 UTC  
> Url: https://github.com/boostorg/math/pull/526#issuecomment-776201248  

So will this put us on a path so that boost.math can be consumed in isolation from the rest of boost?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-02-09 20:08:11 UTC  
> Url: https://github.com/boostorg/math/pull/526#issuecomment-776210354  

>So will this put us on a path so that boost.math can be consumed in isolation from the rest of boost?  
  
Yes.  We would probably need a separate "standalone" mode that disables some features, but yes :)

---
