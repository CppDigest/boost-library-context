# #181 Add checks for extent compatability in span range constructor [Open]

> Username: libbooze  
> Created at: 2024-12-13 19:19:29 UTC  
> Updated at: 2025-03-22 10:59:44 UTC  
> Url: https://github.com/boostorg/core/pull/181  

Based on discussion in https://github.com/boostorg/core/pull/179 / @pdimov  suggestion.  
  
few notes  
1. This will break some current code that does not exibit UB, for example constructing `span` of extent 3 from` boost::array `of size 4. As this is not allowed for `std::array` I presumed same logic should follow for other ranges.  
2. I did not know a nicer C++11 way to produce an error message if extents do not match, so I used this mechanism with conversion of argument to resolve ambiguity  
3. regarding formatting: I found no clang-format file in repo so I did my best to do what seems right  
4. as for tests: I was trying to follow existing practices  
5. lmk if comments are too extensive  
6. if `span_default_constructed_size` works correctly maybe it should be moved to separate header if we think it will be useful in more places  
7. I did not add runtime checks with `BOOST_ASSERT`, I prefer to do that in separate PR  
8. I did not add checks for `max_size`, that may be useful for some fixed buffer types, e.g. [static_string](https://www.boost.org/doc/libs/1_86_0/libs/static_string/doc/html/static_string/ref/boost__static_strings__basic_static_string/max_size.html)  
9. for `std::span` availability in test I used C++23 macro(I presume all C++23 compilers have all C++20 headers) since it is easier than to check for span header, if you prefer I can switch to specific span check  
10. I used `boost::array` as a nice range type, I know core can not depend on almost anything, but I presumed it is fine in tests a I think boost array does not depend on core, if not let me know so I make a simple test type.  
11. `b2 cxxstd=03 test` fails, not sure if that is fine as C++11 is new minimal Boost version

---

## Comment 1

> Username: glenfe  
> Created_at: 2024-12-14 12:44:44 UTC  
> Updated_at: 2024-12-14 12:45:55 UTC  
> Url: https://github.com/boostorg/core/pull/181#issuecomment-2543093212  

I'm not OK with this change. I'll provide rationale when I have some time.

---

## Comment 2

> Username: libbooze  
> Created_at: 2025-03-22 10:59:43 UTC  
> Url: https://github.com/boostorg/core/pull/181#issuecomment-2745211632  

> I'm not OK with this change. I'll provide rationale when I have some time.  
  
If you have some time please let me know if you do not like goals of the change, or if the issue is QoI. If the issue is just QoI I can rework the pull request.

---
