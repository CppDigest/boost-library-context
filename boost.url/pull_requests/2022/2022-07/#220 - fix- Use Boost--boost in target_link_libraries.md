# #220 fix: Use Boost::boost in target_link_libraries [Closed]

> Username: solosTec  
> Created at: 2022-07-04 17:34:07 UTC  
> Updated at: 2022-07-07 20:11:18 UTC  
> Closed at: 2022-07-07 20:11:18 UTC  
> Url: https://github.com/boostorg/url/pull/220  

When using the library in a CMake-based project with FetchContent_Declare() CMake cannot find Boost::align, Boost::asert, etc. because these are header-only libraries. Only Boost::system is a library that can be linked.   
To specify Boost::boost fixed this problem. Then all Boost headers are available.

---

## Comment 1

> Username: alandefreitas  
> Created_at: 2022-07-07 20:11:18 UTC  
> Url: https://github.com/boostorg/url/pull/220#issuecomment-1178169940  

By default, if the library is not root, it will typically assume boost.url is a subdirectory of the boost project, in which case these targets do exist. If these targets didn't exist in any circumstance, we would have easily caught this problem very quickly. The tests wouldn't pass at all.  
  
The build script already supports the case where boost is used as an external library with `BOOST_URL_FIND_PACKAGE_BOOST`. Your changes would reimplement support for the use case when boost is an external library at the expense of breaking/removing the more common use case where boost.url is built as part of boost.  
  
Note that the path you implemented for standalone boost (linking `Boost::boost`) is implemented one command above (linking `Boost::headers`, which is equivalent in this case). This is already implemented. You just need to ensure `BOOST_URL_FIND_PACKAGE_BOOST` is true before fetching contents.  
  
(When `BOOST_URL_FIND_PACKAGE_BOOST` is not set explicitly, we should probably improve the heuristic for `BOOST_URL_FIND_PACKAGE_BOOST`. I'll open an issue for that.)

---
