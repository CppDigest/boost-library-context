# #462 - Missing define BOOST_NO_CXX11_NULLPTR on OSX with libstdc++ [Closed]

> Username: Flamefire  
> Created at: 2022-12-29 18:48:39 UTC  
> Updated at: 2022-12-30 12:00:46 UTC  
> Closed at: 2022-12-30 12:00:45 UTC  
> Url: https://github.com/boostorg/config/issues/462  

A build on XCode 9.4.1 on [Drone](https://drone.cpp.al/boostorg/boost-ci/341/38/2) fails because the standard library (stdlibc++) is lacking the `std::nullptr_t` type.  
  
I believe this means that `BOOST_NO_CXX11_NULLPTR` should be defined but is not. It seems to be possible that the compiler supports the `nullptr` keyword but just that the standard library is to old.  
  
See also https://github.com/CPPAlliance/drone-ci/issues/15

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-12-30 09:16:19 UTC  
> Url: https://github.com/boostorg/config/issues/462#issuecomment-1367810361  

Now fixed in develop.  I did not fix up C++17 compiler support though - the ancient std lib will likely cause unfixable issues.

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-12-30 12:00:45 UTC  
> Url: https://github.com/boostorg/config/issues/462#issuecomment-1367881714  

Thanks!
