# #125 support format [Closed]

> Username: alandefreitas  
> Created at: 2022-02-16 15:51:09 UTC  
> Updated at: 2022-03-16 15:32:35 UTC  
> Closed at: 2022-03-11 20:43:56 UTC  
> Url: https://github.com/boostorg/url/pull/125  

Fixes #62   
  
This is the initial proof of concept. I'm still not sure how to organize this into the test since it involves an external dependency.

---

## Review 1 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-16 15:52:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-884753355  

📁 test/unit/CMakeLists.txt

```diff
  96 |+     FetchContent_MakeAvailable(fmt)
  97 |+     target_link_libraries(boost_url_tests PRIVATE fmt::fmt)
  98 |+     if (CMAKE_CXX_STANDARD GREATER_EQUAL 17)
```

> Username: alandefreitas  
> Created_at: 2022-02-16 15:52:31 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r808151721  

The logic here, for now, was to recur to `__has_include` in C++17 and to a `BOOST_URL_HAS_FMT_FORMAT` macro otherwise.


---

## Review 2 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-16 15:53:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-884755241  

📁 test/unit/CMakeLists.txt

```diff
  89 |+ if (CMAKE_CXX_STANDARD GREATER_EQUAL 14)
  90 |+     include(FetchContent)
  91 |+     FetchContent_Declare(
```

> Username: alandefreitas  
> Created_at: 2022-02-16 15:53:42 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r808153313  

We have to decide what to do here if we are testing this. An alternative here is simply not testing the integration and testing the formatter directly.


---

## Review 3 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-16 15:54:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-884755769  

📁 test/unit/CMakeLists.txt

```diff
  99 |+         target_compile_features(boost_url_tests PRIVATE cxx_std_17) # feature testing
 100 |+     else()
 101 |+         target_compile_features(boost_url_tests PRIVATE cxx_std_14) # return type deduction
```

> Username: alandefreitas  
> Created_at: 2022-02-16 15:54:03 UTC  
> Updated_at: 2022-02-16 15:54:04 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r808153786  

The example in the fmt reference uses auto as the return type.


---

## Review 4 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-16 15:54:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-884756556  

📁 test/unit/url.cpp

```diff
  16 | 
  17 |+ #ifdef __has_include
  18 |+ #if __has_include(<format>)
```

> Username: alandefreitas  
> Created_at: 2022-02-16 15:54:35 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r808154493  

If we are really implementing, probably move it to config.hpp


---

## Review 5 [Commented]

> Username: alandefreitas  
> Created_at: 2022-02-16 15:54:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-884757056  

📁 test/unit/url.cpp

```diff
  34 |+ #ifdef BOOST_URL_HAS_STD_FORMAT
  35 |+ template <>
  36 |+ struct std::formatter<boost::urls::url>
```

> Username: alandefreitas  
> Created_at: 2022-02-16 15:54:54 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r808154881  

If we are really implementing, probably move it to some kind of formatter header somewhere.


---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-02-16 15:56:06 UTC  
> Url: https://github.com/boostorg/url/pull/125#issuecomment-1041813636  

Did we forget about the Jamfile?

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2022-02-16 15:58:06 UTC  
> Updated_at: 2022-02-16 16:00:15 UTC  
> Url: https://github.com/boostorg/url/pull/125#issuecomment-1041816033  

> Did we forget about the Jamfile?  
  
`b2` has any equivalent to fetchcontent? Should we use fetchcontent at all? Using fetchcontent looks like something a boost library wouldn't do.

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2022-02-16 16:52:24 UTC  
> Url: https://github.com/boostorg/url/pull/125#issuecomment-1041872689  

Use of `fetchcontent` is questionable. Also, the Visual Studio project generated from this branch does not work, I get these errors:  
```  
9>The contents of <format> are available only in c++latest mode with concepts support;  
9>see https://github.com/microsoft/STL/issues/1814 for details.  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(36,13): error C2039: 'formatter': is not a member of 'std'  
9>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\vector(24): message : see declaration of 'std'  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(36,22): error C3856: 'formatter': symbol is not a class template  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(36,36): error C2143: syntax error: missing ';' before 'boost::urls::url'  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(36,1): error C2913: explicit specialization; 'formatter' is not a specialization of a class template  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(36,39): error C2059: syntax error: '>'  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(37,12): error C2039: 'formatter': is not a member of 'std'  
9>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\vector(24): message : see declaration of 'std'  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(37,21): error C2059: syntax error: '<'  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(37,27): error C2039: 'string_view': is not a member of 'std'  
9>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\vector(24): message : see declaration of 'std'  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(38,1): error C2143: syntax error: missing ';' before '{'  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(38,1): error C2447: '{': missing function header (old-style formal list?)  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(1868,35): error C2039: 'format': is not a member of 'std'  
9>C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.29.30133\include\vector(24): message : see declaration of 'std'  
9>C:\Users\vinnie\src\boost\libs\url\test\unit\url.cpp(1868,1): error C3861: 'format': identifier not found  
9>Done building project "boost_url_tests.vcxproj" -- FAILED.  
11>------ Build started: Project: ALL_BUILD, Configuration: Debug x64 ------  
12>------ Skipped Build: Project: tests, Configuration: Debug x64 ------  
12>Project not selected to build for this solution configuration   
13>------ Skipped Build: Project: INSTALL, Configuration: Debug x64 ------  
13>Project not selected to build for this solution configuration   
========== Build: 5 succeeded, 1 failed, 0 up-to-date, 7 skipped ==========  
```  
  
I think we should probably only support `<format>`, conditionally compiled for C++20 (?) and maybe not worry about the standalone fmt library for now.

---

## Comment 9

> Username: alandefreitas  
> Created_at: 2022-02-16 20:40:15 UTC  
> Url: https://github.com/boostorg/url/pull/125#issuecomment-1042270150  

Do we need a function to return a `string_view` from a `url_view`?  
  
`std::string_view(u.c_str(), u.size())` looks ugly.

---

## Comment 10

> Username: vinniefalco  
> Created_at: 2022-02-17 19:47:21 UTC  
> Updated_at: 2022-02-17 19:47:58 UTC  
> Url: https://github.com/boostorg/url/pull/125#issuecomment-1043354597  

> Do we need a function to return a string_view from a url_view?  
  
What about `url_view::string()` ?  
  
https://master.url.cpp.al/url/ref/boost__urls__url_view/string.html

---

## Review 11 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-17 19:49:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-886477119  

📁 include/boost/url/url.hpp

```diff
1421 |+ */
1422 |+ template <>
1423 |+ struct std::formatter<boost::urls::url>;
```

> Username: vinniefalco  
> Created_at: 2022-02-17 19:49:12 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r809414733  

Use `::boost::urls::url`


---

## Review 12 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-17 19:51:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-886479584  

📁 include/boost/url/url.hpp

```diff
1421 |+ */
1422 |+ template <>
1423 |+ struct std::formatter<boost::urls::url>;
```

> Username: vinniefalco  
> Created_at: 2022-02-17 19:51:39 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r809416541  

I don't think this should be part of the reference


---

## Review 13 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-17 19:52:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-886480084  

📁 include/boost/url/impl/url.ipp

```diff
2347 |+ template <>
2348 |+ struct std::formatter<boost::urls::url>
2349 |+     : std::formatter<std::string_view>
```

> Username: vinniefalco  
> Created_at: 2022-02-17 19:52:02 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r809416822  

You will have to include `<string_view>` for this, no?


---

## Review 14 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-17 19:52:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-886480386  

📁 include/boost/url/impl/url.ipp

```diff
2360 | #endif
2361 |+ 
2362 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2022-02-17 19:52:12 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r809416936  

missing newline


---

## Review 15 [Commented]

> Username: vinniefalco  
> Created_at: 2022-02-17 19:52:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/url/pull/125#pullrequestreview-886480559  

📁 include/boost/url/impl/url.ipp

```diff
2355 |+     {
2356 |+         return formatter<string_view>::format(
2357 |+             std::string_view(u.c_str(), u.size()), ctx);
```

> Username: vinniefalco  
> Created_at: 2022-02-17 19:52:22 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r809417070  

`u.string()`

> Username: vinniefalco  
> Created_at: 2022-02-17 19:52:52 UTC  
> Url: https://github.com/boostorg/url/pull/125#discussion_r809417567  

Also you should use `u.data()` instead of `u.c_str()` (if you wanted the pointer and size)


---

## Comment 16

> Username: alandefreitas  
> Created_at: 2022-02-17 20:14:26 UTC  
> Url: https://github.com/boostorg/url/pull/125#issuecomment-1043381821  

> What about url_view::string() ?  
  
At first, I assumed it would return a `std::string`. But then I realized ensuring std::string_view would be better anyway.

---

## Comment 17

> Username: alandefreitas  
> Created_at: 2022-03-11 20:38:23 UTC  
> Url: https://github.com/boostorg/url/pull/125#issuecomment-1065500787  

I've been wondering we should really keep pursuing this PR. It's applicability seems very limited:  
  
- For a given `url` or `url_view`, the user can always `fmt::format("{}", u.string())`. The formatter is just a long way to replicate this logic.  
- The `fmt` is the only implementation people seem to be using in practice. It's also the best implementation. The `fmt` formatter worked perfectly in tests, but integrating a non-boost external dependency just for that is controversial, to say the least.   
- The formatter is just a customization point, so we could just leave the formatter there. But then there would be no integration tests.  
- GCC and Clang don't implement `std::format` yet, so MSVC code using `std::format` will not work everywhere.  
- The MSVC implementation has a bug where the `string_view` formatter is not `const`, so the usual strategy of inheriting from `string_view` doesn't work. This would require us to reimplement the whole `string_view` formatter, which is probably the most complex formatter, since it handles all formatting options available in `fmt`.

---

## Comment 18

> Username: vinniefalco  
> Created_at: 2022-03-11 21:09:09 UTC  
> Url: https://github.com/boostorg/url/pull/125#issuecomment-1065524883  

tl;dr: too much try-hard for too little reward

---
