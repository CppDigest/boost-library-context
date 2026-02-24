# #118 fix msvc c++17 std::invoke_result config [Closed]

> Username: Neargye  
> Created at: 2018-05-25 15:23:26 UTC  
> Updated at: 2019-06-17 15:58:21 UTC  
> Closed at: 2019-06-17 15:58:21 UTC  
> Url: https://github.com/boostorg/asio/pull/118  

* use _MSVC_LANG  instead _HAS_CXX17.  
* [std::invoke_result was implemented in 15.3 (_MSC_VER == 1911)](https://blogs.msdn.microsoft.com/vcblog/2017/08/11/c17-features-and-stl-fixes-in-vs-2017-15-3)  
* for version less VS 2017.3, used std::result_of with suppress warning

---
