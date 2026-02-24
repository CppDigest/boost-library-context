# #225 - Consider switching dependency from MPL to MP11 [Open]

> Username: pwiecz  
> Created at: 2024-01-15 10:35:57 UTC  
> Updated at: 2025-07-17 08:25:03 UTC  
> Url: https://github.com/boostorg/log/issues/225  

Hi,  
I'm suffering slow build times of my c++ application.  
Profiling compilation times shows that significant percentage of time is spent processing MPL headers.  
I wonder if the Boost::Log team considered switching from MPL to MP11 and if, in your experience, it could bring significant compilation time improvements?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-01-15 11:10:38 UTC  
> Url: https://github.com/boostorg/log/issues/225#issuecomment-1891940224  

I didn't perform any compilation time comparisons between Boost.MPL and Boost.MP11, but I've seen reports that the latter performs better. I didn't see any numbers, though.  
  
I did think about switching to Boost.MP11, but this would be an API breaking change, as Boost.Log supports user-specified MPL sequences in a few places. I'm not sure if this can be supported in a backwards-compatible way without the dependency on MPL, but it certainly makes the switch not so simple.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-01-15 11:13:53 UTC  
> Url: https://github.com/boostorg/log/issues/225#issuecomment-1891950092  

I should also note that Boost.Log filter and formatting expressions are based on Boost.Phoenix, which relies on Boost.Fusion, which, in turn, uses Boost.MPL for metaprogramming. So even if Boost.Log itself makes the switch, I don't think this will remove the dependency on Boost.MPL.

---

## Comment 3

> Username: sdebionne  
> Created at: 2025-07-17 06:42:55 UTC  
> Url: https://github.com/boostorg/log/issues/225#issuecomment-3082784444  

This makes me wonder if a V3 is in the pipeline which would require C++11 (and might help eliminate much of the library's complexity)?

---

## Comment 4

> Username: Lastique  
> Created at: 2025-07-17 08:25:03 UTC  
> Url: https://github.com/boostorg/log/issues/225#issuecomment-3083119562  

Yes, in a theoretical Boost.Log v3 I would have done a few things differently, and it would be based on a more recent C++ version. I think, a new logging library with a different, more compile-time rather than run-time focused design could be useful. But currently I'm not working on it.
