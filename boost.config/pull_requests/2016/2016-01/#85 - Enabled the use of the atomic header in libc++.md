# #85 Enabled the use of the atomic header in libc++ [Closed]

> Username: ylow  
> Created at: 2016-01-01 00:24:47 UTC  
> Updated at: 2016-01-01 16:02:39 UTC  
> Closed at: 2016-01-01 13:36:18 UTC  
> Url: https://github.com/boostorg/config/pull/85  

BOOST_NO_CXX11_HDR_ATOMIC appears to be defined always when libc++ is used.  
  
Looking at the rationale http://lists.boost.org/Archives/boost/2014/02/211902.php , it references the following bug in libc++ https://llvm.org/bugs/show_bug.cgi?id=18097 . This bug appears to be fixed. At least, on my machine (OS X (Apple LLVM version 7.0.2 (clang-700.1.81)) with _LIBCPP_VERSION == 1101) all the failure tests on that bug report appears to pass.  
  
An annoyance is that the libc++ version does not appear to be bumped consistently. Before the fix, _LIBCPP_VERSION==1101, and after the fix, _LIBCPP_VERSION is still 1101 for quite a while looking at http://llvm.org/viewvc/llvm-project/libcxx/trunk/include/__config?view=log  
  
Testing against _LIBCPP_VERSION < 3700 thus seems to be the safest bet even though it will exclude my current machine.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-01-01 13:36:18 UTC  
> Url: https://github.com/boostorg/config/pull/85#issuecomment-168306674  

I applied your fix slightly differently here: https://github.com/boostorg/config/commit/af5351f42beed4dcadf43bd8a03d912836aa9f7b  
  
Thanks!

---

## Comment 2

> Username: apolukhin  
> Created_at: 2016-01-01 15:57:56 UTC  
> Url: https://github.com/boostorg/config/pull/85#issuecomment-168313984  

> I applied your fix slightly differently here: af5351f  
  
Seems like there's some issue: `BOOST_NO_CXX11_HDR_ATOMIC` must be unconditionally defined if `__cplusplus < 201103`, and defined in all other cases if `_LIBCPP_VERSION < 3700`

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2016-01-01 16:02:39 UTC  
> Url: https://github.com/boostorg/config/pull/85#issuecomment-168314109  

> Seems like there's some issue: |BOOST_NO_CXX11_HDR_ATOMIC| must be   
> unconditionally defined if |__cplusplus < 201103|, and defined in all   
> other cases if |_LIBCPP_VERSION < 3700|  
  
You're right that PP-logic is wrong, second try:   
https://github.com/boostorg/config/commit/b1df465870205a007fb6766ff9318c6ec27e6346  
  
Thanks, John.

---
