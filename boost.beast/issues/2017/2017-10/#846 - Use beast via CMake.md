# #846 - Use beast via CMake [Closed]

> Username: EdvardD  
> Created at: 2017-10-28 17:06:20 UTC  
> Updated at: 2017-10-30 12:46:29 UTC  
> Closed at: 2017-10-30 12:46:29 UTC  
> Url: https://github.com/boostorg/beast/issues/846  

Is it possible to use beast from my project by CMake command find_package?

---

## Comment 1

> Username: djarek  
> Created at: 2017-10-28 17:16:16 UTC  
> Url: https://github.com/boostorg/beast/issues/846#issuecomment-340206250  

```  
find_package(Boost 1.66 COMPONENTS system REQUIRED)  
```  
  
This is sufficient (please note that cmake scripts don't support Boost 1.66 yet and will present a warning:  
  
```  
-- clang-format found: /usr/bin/clang-format  
CMake Warning at /usr/share/cmake-3.7/Modules/FindBoost.cmake:761 (message):  
  Imported targets not available for Boost version 106600  
Call Stack (most recent call first):  
  /usr/share/cmake-3.7/Modules/FindBoost.cmake:865 (_Boost_COMPONENT_DEPENDENCIES)  
  /usr/share/cmake-3.7/Modules/FindBoost.cmake:1470 (_Boost_MISSING_DEPENDENCIES)  
  CMakeLists.txt:6 (find_package)  
  
  
-- Boost version: 1.66.0  
-- Found the following Boost libraries:  
--   system  
*** Finished ***  
  
```  
  
You can safely ignore the warnings.

---

## Comment 2

> Username: EdvardD  
> Created at: 2017-10-29 17:34:18 UTC  
> Url: https://github.com/boostorg/beast/issues/846#issuecomment-340279431  

I use Boost 1.58 and it's difficult to update it (a lot of services should be updated). Is it possible to use Beast with Boost 1.58?  
I find out that Beast was compatible with Boost 1.58 until previous week.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-10-29 17:41:26 UTC  
> Url: https://github.com/boostorg/beast/issues/846#issuecomment-340279906  

If you want to use Beast with Boost 1.58 you should use the **v124** tag: https://github.com/boostorg/beast/tree/v124

---

## Comment 4

> Username: EdvardD  
> Created at: 2017-10-30 12:46:29 UTC  
> Url: https://github.com/boostorg/beast/issues/846#issuecomment-340433385  

Thanks
