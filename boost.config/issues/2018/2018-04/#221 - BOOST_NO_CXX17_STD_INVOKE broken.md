# #221 - BOOST_NO_CXX17_STD_INVOKE broken [Closed]

> Username: NAThompson  
> Created at: 2018-04-03 08:19:54 UTC  
> Updated at: 2018-04-18 18:31:46 UTC  
> Closed at: 2018-04-18 18:31:45 UTC  
> Url: https://github.com/boostorg/config/issues/221  

`BOOST_NO_CXX17_STD_INVOKE` does not work on g++-6.4.0 or Apple LLVM version 9.1.0 (clang-902.0.39.1) in C++1z mode, due to the fact that their implementation of C++17 is incomplete.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-04-03 18:10:52 UTC  
> Url: https://github.com/boostorg/config/issues/221#issuecomment-378344757  

Confirmed for GCC-6.x.  I've updated the test case in develop to check for `invoke_result`, and updated the GCC and msvc configurations.  clang+libc++ will have to wait till I see what fails in the CI tests.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-04-05 09:56:47 UTC  
> Url: https://github.com/boostorg/config/issues/221#issuecomment-378883015  

See also https://github.com/boostorg/config/pull/98

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-04-18 18:31:45 UTC  
> Url: https://github.com/boostorg/config/issues/221#issuecomment-382485422  

Closing as this should be fixed now.
