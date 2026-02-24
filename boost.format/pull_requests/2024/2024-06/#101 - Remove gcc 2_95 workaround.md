# #101 Remove gcc 2_95 workaround. [Open]

> Username: gogagum  
> Created at: 2024-06-07 19:06:46 UTC  
> Updated at: 2024-07-18 07:28:06 UTC  
> Url: https://github.com/boostorg/format/pull/101  

`C++11` is now a minimal required C++ version for `boost::format`. This means that old `gcc` version (older then `gcc 4.x`) support is no longer required. This PR removes unused workaround for `std::char_traits` and `std::allocator`, which was made for `gcc` `2.95`.

---

## Comment 1

> Username: jeking3  
> Created_at: 2024-07-12 21:20:14 UTC  
> Url: https://github.com/boostorg/format/pull/101#issuecomment-2226369045  

Why didn't this run all the CI tests? :|

---

## Comment 2

> Username: gogagum  
> Created_at: 2024-07-15 19:59:15 UTC  
> Updated_at: 2024-07-18 07:28:06 UTC  
> Url: https://github.com/boostorg/format/pull/101#issuecomment-2229278382  

@jeking3 I turned on CI runs on my fork initially, so github decided not to run tests again, when the pull request was made. So the lack of CI runs is not caused by changes. Now I turned off tests in my fork and reran CI. [appveyor CI](https://ci.appveyor.com/project/jeking3/format-bhjc4/builds/50216202) succeded. [Github CI](https://github.com/boostorg/format/actions/runs/9945260831) now has a requirement of approval from a maintainer.

---
