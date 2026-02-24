# #98 - [OPEN] Missing header files or incorrect path? [Closed]

> Username: johnalupton  
> Created at: 2018-03-28 11:20:42 UTC  
> Updated at: 2020-12-30 00:50:13 UTC  
> Closed at: 2020-12-30 00:50:12 UTC  
> Url: https://github.com/boostorg/asio/issues/98  

Hi - I just cloned the current master version and am trying a test compile of a "Hello world", just to check my basic installation is ok before i start working through the tutorial.  
  
My problem is that **a number of sub-include header files can not be found by asio.hpp** 😕   
  
**Pre-compile error thrown**  
`cannot open source file "boost/config.hpp" (dependency of "boost/asio/associated_allocator.hpp")`  
  
The only `config.hpp` file that I can find resides in   
`<project root dir>\asio\include\boost\asio\detail\config.hpp`  
  
  
**Set up process**  
I added latest master [boostorg/asio.git](https://github.com/boostorg/asio/commit/c30ed33c5edf5b8d2bdbbfec125139889d69e136) following github instuctions on how [work with submodules](https://blog.github.com/2016-02-01-working-with-submodules/)  
  
`git submodule add https://github.com/boostorg/asio.git asio`  
  
and recursively added the submodule beneath my project root directory  
  
`git submodule update --init --recursive`  
  
This has created the directory structure  
  
`<project root dir>/asio/include/boost/...`

---

## Comment 1

> Username: johnalupton  
> Created at: 2018-03-28 16:23:10 UTC  
> Url: https://github.com/boostorg/asio/issues/98#issuecomment-376948080  

From the boost website, I added the following files beneath the directory   
`<project root dir>/asio/include/boost/`  
  
[loads more than this ...]  
  
http://www.boost.org/doc/libs/1_66_0/boost/assert.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/config.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/cstdint.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/is_placeholder.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/noncopyable.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/limits.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/regex_fwd.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/throw_exception.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/version.hpp  
  
http://www.boost.org/doc/libs/1_66_0/boost/bind/arg.hpp  
  
http://www.boost.org/doc/libs/1_66_0/boost/config/auto_link.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/config/workaround.hpp  
  
http://www.boost.org/doc/libs/1_66_0/boost/config/detail/select_compiler_config.hpp  
  
http://www.boost.org/doc/libs/1_66_0/boost/detail/workaround.hpp  
  
http://www.boost.org/doc/libs/1_66_0/boost/date_time/posix_time/posix_time.hpp  
  
http://www.boost.org/doc/libs/1_66_0/boost/system/api_config.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/system/config.hpp  
http://www.boost.org/doc/libs/1_66_0/boost/system/error_code.hpp  
  
http://www.boost.org/doc/libs/1_66_0/boost/predef/platform.h  
http://www.boost.org/doc/libs/1_66_0/boost/predef/version_number.h  
  
http://www.boost.org/doc/libs/1_66_0/boost/predef/platform/  
  
http://www.boost.org/doc/libs/1_66_0/boost/predef/os/ios.h  
http://www.boost.org/doc/libs/1_66_0/boost/predef/os/windows.h  
  
http://www.boost.org/doc/libs/1_66_0/boost/predef/detail/os_detected.h  
http://www.boost.org/doc/libs/1_66_0/boost/predef/detail/platform_detected.h  
http://www.boost.org/doc/libs/1_66_0/boost/predef/detail/test.h

---

## Comment 2

> Username: johnalupton  
> Created at: 2018-03-29 10:01:34 UTC  
> Url: https://github.com/boostorg/asio/issues/98#issuecomment-377186781  

Ok, so I spent hours not getting a clean compile of a test program that simply included asio: Are there any install guides anywhere? I have tried finding something to no avail ... 😕

---

## Comment 3

> Username: purefan  
> Created at: 2018-06-09 20:27:51 UTC  
> Url: https://github.com/boostorg/asio/issues/98#issuecomment-395996420  

none that I have found, makes the whole asio thing feel like dark magic

---

## Comment 4

> Username: Skillotic0703  
> Created at: 2018-06-28 12:17:17 UTC  
> Url: https://github.com/boostorg/asio/issues/98#issuecomment-401014799  

I have the same problem! There is no file config.hpp in the directory boost

---

## Comment 5

> Username: jeking3  
> Created at: 2018-11-20 14:05:25 UTC  
> Url: https://github.com/boostorg/asio/issues/98#issuecomment-440284493  

Some time this past year, Boost.Build was changed to automatically execute the "./b2 headers" step which it looks like you have have omitted.  This would have caused your missing header in the past.

---

## Comment 6

> Username: ghost  
> Created at: 2020-12-30 00:50:11 UTC  
> Url: https://github.com/boostorg/asio/issues/98#issuecomment-752289288  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#605](https://github.com/chriskohlhoff/asio/issues/605).
