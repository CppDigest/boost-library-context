# #287 - Should consider the value of __cplusplus for macro definations in boost/config/detail/suffix.hpp [Closed]

> Username: BinCaoWR  
> Created at: 2019-06-17 06:48:12 UTC  
> Updated at: 2019-10-29 09:29:15 UTC  
> Closed at: 2019-10-29 09:29:15 UTC  
> Url: https://github.com/boostorg/config/issues/287  

Boost detects the availability of features by checking for the presence of the corresponding headers in boost/config/detail/suffix.hpp, but not checking the value of the __cplusplus macro.  
This results in issues if we have multiple C++ versions built.  
  
For example, if we have C++14 & C++17 built before building boost. That is, most of the C++17 headers are available.  
Now we want to build boost with -std=c++14. That is, it is expected that boost should only use the C++14 functionality.  
Howerver, boost reports following build errors:  
  
In file included from ../libs/config/test/config_test.cpp:351:  
../libs/config/test/boost_no_cxx17_hdr_string_view.ipp:19:14: error: no member named 'string_view' in namespace 'std'  
  using std::string_view;  
        ~~~~~^  
  
This is because, in boost/config/detail/suffix.hpp, BOOST_NO_CXX17_HDR_STRING_VIEW is not defined in this case.  
  
#if !defined(__has_include)  
#define BOOST_NO_CXX17_HDR_OPTIONAL  
#define BOOST_NO_CXX17_HDR_STRING_VIEW  
#define BOOST_NO_CXX17_HDR_VARIANT  
#else  
#if !__has_include(<optional>)  
#define BOOST_NO_CXX17_HDR_OPTIONAL  
#endif  
#if !__has_include(<string_view>)  
#define BOOST_NO_CXX17_HDR_STRING_VIEW  
#endif  
#if !__has_include(<variant>)  
#define BOOST_NO_CXX17_HDR_VARIANT  
#endif  
#endif  
  
This should be changed to something like:  
  
#if !defined(__has_include) || __cplusplus < 201700  
#define BOOST_NO_CXX17_HDR_OPTIONAL  
#define BOOST_NO_CXX17_HDR_STRING_VIEW  
#define BOOST_NO_CXX17_HDR_VARIANT  
#else  
...
