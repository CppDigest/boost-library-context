# #1673 - Boost/beast failed due to error C2039 under /permissive- + VS PREVIEW Edition [Closed]

> Username: QuellaZhang  
> Created at: 2019-08-02 06:32:37 UTC  
> Updated at: 2019-10-04 13:11:26 UTC  
> Closed at: 2019-10-04 13:11:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1673  

**Environment:**  
VS 2017 + Windows Server 2016  
  
**Issue description:**  
We build and run test for Boost and we found one test Boost/beast failed to run under /permissive- mode. Could you please take a look?  
  
R**eproduce steps:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git D:\Boost\src  
2. open a VS 2017 x64 command prompt and browse to D:\Boost\src  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x64rel address-model=64  
5. .\b2 variant=release --build-dir=..\out\x64rel address-model=64  
6. .\b2 -j4 variant=release --build-dir=..\out\x64rel libs\beast\test  
  
Build log: [log_x64_test_124.log](https://github.com/boostorg/beast/files/3459883/log_x64_test_124.log)  
  
  
**Failures:**  
.\boost/beast/http/impl/file_body_win32.hpp(369): error C2039: 'async_write_some_impl': is not a member of 'boost::beast::http::detail'  
.\boost/beast/http/impl/file_body_win32.hpp(279): note: see declaration of 'boost::beast::http::detail'  
.\boost/beast/http/impl/file_body_win32.hpp(437): note: see reference to class template instantiation 'boost::beast::http::detail::write_some_win32_op<Protocol,Executor,isRequest,Fields,Handler>' being compiled  
.\boost/beast/core/string.hpp(56): note: see reference to class template instantiation 'boost::basic_string_view<char,std::char_traits<char>>' being compiled  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\VC\Tools\MSVC\14.16.27023\include\chrono(671): note: see reference to class template instantiation 'std::chrono::duration<double,std::ratio<1,1>>' being compiled  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\VC\Tools\MSVC\14.16.27023\include\chrono(178): note: see reference to class template instantiation 'std::chrono::duration<__int64,std::nano>' being compiled  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\VC\Tools\MSVC\14.16.27023\include\chrono(649): note: see reference to class template instantiation 'std::chrono::time_point<std::chrono::steady_clock,std::chrono::steady_clock::duration>' being compiled  
.\boost/beast/http/impl/file_body_win32.hpp(374): error C2039: 'async_write_some_impl': is not a member of 'boost::beast::http::detail'  
.\boost/beast/http/impl/file_body_win32.hpp(279): note: see declaration of 'boost::beast::http::detail'  
.\boost/beast/http/impl/file_body_win32.hpp(491): error C2039: 'write_some_impl': is not a member of 'boost::beast::http::detail'  
.\boost/beast/http/impl/file_body_win32.hpp(279): note: see declaration of 'boost::beast::http::detail'  
.\boost/beast/http/impl/file_body_win32.hpp(491): note: This diagnostic occurred in the compiler generated function 'size_t boost::beast::http::write_some(boost::asio::basic_stream_socket<Protocol,Executor> &,boost::beast::http::serializer<isRequest,boost::beast::http::basic_file_body<boost::beast::file>,Fields> &,boost::beast::error_code &)'  
.\boost/beast/http/impl/file_body_win32.hpp(499): error C2039: 'write_some_impl': is not a member of 'boost::beast::http::detail'  
.\boost/beast/http/impl/file_body_win32.hpp(279): note: see declaration of 'boost::beast::http::detail'  
.\boost/beast/http/impl/file_body_win32.hpp(499): note: This diagnostic occurred in the compiler generated function 'size_t boost::beast::http::write_some(boost::asio::basic_stream_socket<Protocol,Executor> &,boost::beast::http::serializer<isRequest,boost::beast::http::basic_file_body<boost::beast::file>,Fields> &,boost::beast::error_code &)'

---

## Comment 1

> Username: stale[bot]  
> Created at: 2019-09-01 07:02:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1673#issuecomment-526893006  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-09-09 23:23:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1673#issuecomment-529704524  

@QuellaZhang is this still a problem? I am not able to reproduce it in the latest Boost

---

## Comment 3

> Username: cstratopoulos  
> Created at: 2019-09-24 16:10:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1673#issuecomment-534630951  

This issue has popped up again in the latest VS2019 preview release.  
  
Visual Studio developer community issue: https://developercommunity.visualstudio.com/content/problem/745888/visual-studio-1640-preview-10-has-regression-with.html  
  
Short version: Removing `/permissive-` from the command line gives a temporary fix, but that may not be feasible in general if you're bundling a Beast project with other dependencies which set that flag

---

## Comment 4

> Username: magicalcookie  
> Created at: 2019-09-25 10:48:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1673#issuecomment-534965209  

Adding `#include <boost/beast/http/write.hpp>` to `include/boost/beast/http/impl/file_body_win32.hpp` fixed the error for us.
