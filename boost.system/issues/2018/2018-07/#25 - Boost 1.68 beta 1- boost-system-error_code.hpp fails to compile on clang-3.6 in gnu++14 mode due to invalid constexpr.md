# #25 - Boost 1.68 beta 1: boost/system/error_code.hpp fails to compile on clang-3.6 in gnu++14 mode due to invalid constexpr [Closed]

> Username: rainerdeyke  
> Created at: 2018-07-29 14:53:42 UTC  
> Updated at: 2018-09-21 15:59:19 UTC  
> Closed at: 2018-09-21 15:59:19 UTC  
> Url: https://github.com/boostorg/system/issues/25  

(This is a duplicate of issue #13633 on the main Boost bug tracker, which has been sitting ignored for two weeks now.  Is this a better place to submit Boost.System errors?)  
  
  
  
A minimal test file that includes "boost/system/error_code.hpp" fails to compile on my clang-3.6 in gnu++14 mode with the Boost 1.68.0 beta 1. The exact command line and error message are:  
  
rainer@rainer10:~/work/cpp_test$ schroot -c steamrt_scout_amd64 -- clang-3.6 -std=gnu++14 -c test.cpp -I boost_1_68_0/ In file included from test.cpp:1: boost_1_68_0/boost/system/error_code.hpp:226:41: error: constexpr constructor  
  
    never produces a constant expression [-Winvalid-constexpr]  
  
        BOOST_SYSTEM_CONSTEXPR explicit std_category( boost::system::err...  
  
boost_1_68_0/boost/system/error_code.hpp:226:41: note: non-constexpr constructor  
  
    'error_category' cannot be used in a constant expression  
  
/usr/bin/../lib/gcc/x86_64-linux-gnu/4.8/../../../../include/c++/4.8/system_error:69:5: note:  
  
    declared here  
  
    error_category() noexcept;  
  
1 error generated.  
  
This is a regression from Boost 1.67.  
[error.txt](https://github.com/boostorg/system/files/2238869/error.txt)  
[test.cpp.txt](https://github.com/boostorg/system/files/2238870/test.cpp.txt)

---

## Comment 1

> Username: pdimov  
> Created at: 2018-07-29 16:41:39 UTC  
> Url: https://github.com/boostorg/system/issues/25#issuecomment-408689823  

This should be fixed in https://github.com/boostorg/system/commit/3c59cea7ef0d45330a0c227967c071d89e5fb4b6, which is currently part of the master branch and should be in the final release.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-07-29 16:42:30 UTC  
> Url: https://github.com/boostorg/system/issues/25#issuecomment-408689876  

>  Is this a better place to submit Boost.System errors?  
  
Yes please. :-)
