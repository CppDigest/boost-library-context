# #737 - Compile Error when building Boost with cmake [Closed]

> Username: madmongo1  
> Created at: 2022-08-09 07:55:13 UTC  
> Updated at: 2022-08-14 10:23:51 UTC  
> Closed at: 2022-08-14 10:23:51 UTC  
> Url: https://github.com/boostorg/json/issues/737  

PLEASE DON'T FORGET TO "STAR" THIS REPOSITORY :)  
  
When reporting a bug please include the following:  
  
### Version of Boost  
  
Boost-1.80.0.beta1  
  
### Steps necessary to reproduce the problem  
  
Build the boost repository under cmake with GCC12.1  
  
When building boost, the following compile error is emitted:  
  
```  
[build] In file included from /Users/rhodges/github/madmongo1/blog-2022-Aug-websock-redirect/build/_deps/boost-src/libs/json/include/boost/json/src.hpp:37,  
[build]                  from /Users/rhodges/github/madmongo1/blog-2022-Aug-websock-redirect/build/_deps/boost-src/libs/json/src/src.cpp:10:  
[build] /Users/rhodges/github/madmongo1/blog-2022-Aug-websock-redirect/build/_deps/boost-src/libs/json/include/boost/json/impl/parse.ipp: In function 'boost::json::value boost::json::parse(string_view, std::error_code&, storage_ptr, const parse_options&)':  
[build] /Users/rhodges/github/madmongo1/blog-2022-Aug-websock-redirect/build/_deps/boost-src/libs/json/include/boost/json/impl/parse.ipp:46:10: error: no match for 'operator=' (operand types are 'std::error_code' and 'boost::json::error_code' {aka 'boost::system::error_code'})  
[build]    46 |     ec = jec;  
[build]       |          ^~~  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-08-09 08:02:06 UTC  
> Url: https://github.com/boostorg/json/issues/737#issuecomment-1209047035  

This appears to be an issue with Boost.System. @pdimov are you aware of this problem?

---

## Comment 2

> Username: pdimov  
> Created at: 2022-08-09 08:07:19 UTC  
> Url: https://github.com/boostorg/json/issues/737#issuecomment-1209052024  

Probably using an old System found via `find_package`.

---

## Comment 3

> Username: madmongo1  
> Created at: 2022-08-09 08:27:58 UTC  
> Url: https://github.com/boostorg/json/issues/737#issuecomment-1209075228  

```  
FetchContent_Declare(boost  
    GIT_REPOSITORY https://github.com/boostorg/boost.git  
    GIT_TAG boost-1.80.0.beta1  
    GIT_SHALLOW ON  
    GIT_PROGRESS ON  
)  
message(STATUS "[dependency] building boost")  
FetchContent_MakeAvailable(boost)  
```

---

## Comment 4

> Username: madmongo1  
> Created at: 2022-08-14 10:23:51 UTC  
> Url: https://github.com/boostorg/json/issues/737#issuecomment-1214329904  

Requires latest master branch.  
Solved.
