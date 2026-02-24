# #30 replace deprecated binders and adapters, and random_shuffle by more m… [Closed]

> Username: DanielaE  
> Created at: 2016-11-06 15:41:07 UTC  
> Updated at: 2017-04-22 16:40:53 UTC  
> Closed at: 2016-11-21 01:25:48 UTC  
> Url: https://github.com/boostorg/algorithm/pull/30  

**replace deprecated binders and adapters, and random_shuffle by more modern equivalents**  
  
Required with latest msvc versions and compiler option /std:c++latest

---

## Comment 1

> Username: mclow  
> Created_at: 2016-11-07 15:14:16 UTC  
> Url: https://github.com/boostorg/algorithm/pull/30#issuecomment-258862159  

I don't think this is sufficient, for a couple reasons.  
1) Just removing the inheritance for `unary_function` and `binary_function` will break code that looks for the nested typedefs `result_type`, etc.  These need to be preserved.  Please see my emails on the boost mailing lists about this.  
2) Blindly replacing `shuffle` with `random_shuffle` will also break code.  People who aren't building with C++1z enabled will not see `random_shuffle`, and their compiles will fail. Some kind of conditional compilation is needed here.

---

## Comment 2

> Username: mclow  
> Created_at: 2016-11-21 01:01:55 UTC  
> Url: https://github.com/boostorg/algorithm/pull/30#issuecomment-261820470  

I was at least (partially) wrong here; removing the dependencies on `(unary|binary)_function` is not a problem at all. All the uses are internal.

---

## Comment 3

> Username: mclow  
> Created_at: 2016-11-21 01:25:48 UTC  
> Url: https://github.com/boostorg/algorithm/pull/30#issuecomment-261822352  

I have made a similar change; calling `std::shuffle` in C++11 and later, and `std::random_shuffle` in C++03 code. Closing this P/R. Thanks for the help.

---
