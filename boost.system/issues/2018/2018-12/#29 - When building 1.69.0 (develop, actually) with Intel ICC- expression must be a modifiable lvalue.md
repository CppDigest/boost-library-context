# #29 - When building 1.69.0 (develop, actually) with Intel ICC: expression must be a modifiable lvalue [Closed]

> Username: jeking3  
> Created at: 2018-12-21 22:06:22 UTC  
> Updated at: 2018-12-22 00:34:56 UTC  
> Closed at: 2018-12-21 22:36:17 UTC  
> Url: https://github.com/boostorg/system/issues/29  

I am adding Intel ICC build support into boostorg/boost-ci and when I tried to build Boost.Uuid, it failed with this error:  
```  
intel-linux.compile.c++ ../../bin.v2/libs/timer/build/intel-linux/release/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/cpu_timer.o  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(451): error #137: expression must be a modifiable lvalue  
          val_ = val;  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(452): error #137: expression must be a modifiable lvalue  
          failed_ = detail::failed_impl( val, cat );  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(453): error #137: expression must be a modifiable lvalue  
          cat_ = &cat;  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(466): error #137: expression must be a modifiable lvalue  
          val_ = 0;  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(467): error #137: expression must be a modifiable lvalue  
          failed_ = false;  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(468): error #137: expression must be a modifiable lvalue  
          cat_ = &generic_category();  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(587): error #137: expression must be a modifiable lvalue  
          val_ = val;  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(588): error #137: expression must be a modifiable lvalue  
          failed_ = detail::failed_impl( val, cat );  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(589): error #137: expression must be a modifiable lvalue  
          cat_ = &cat;  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(602): error #137: expression must be a modifiable lvalue  
          val_ = 0;  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(603): error #137: expression must be a modifiable lvalue  
          failed_ = false;  
          ^  
In file included from ../../boost/chrono/detail/system.hpp(11),  
                 from ../../boost/chrono/system_clocks.hpp(64),  
                 from ../../boost/chrono/chrono.hpp(13),  
                 from ../../libs/timer/src/cpu_timer.cpp(17):  
../../boost/system/error_code.hpp(604): error #137: expression must be a modifiable lvalue  
          cat_ = &system_category();  
          ^  
compilation aborted for ../../libs/timer/src/cpu_timer.cpp (code 2)  
```  
  
No further analysis has been done.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-12-21 22:11:38 UTC  
> Url: https://github.com/boostorg/system/issues/29#issuecomment-449511033  

#### Additional Details:  
  
The new file in Boost-CI:  
https://github.com/boostorg/boost-ci/blob/master/ci/travis/intelicc.sh  
  
The pull request in Boost.Uuid (my fork):  
https://github.com/jeking3/uuid/pull/19  
  
The version of Intel ICC provided by Boost.Config:  
```  
    __ICC                                   =1900  
    __INTEL_COMPILER                        =1900  
    __INTEL_COMPILER_BUILD_DATE             =20181018  
```  
  
This is part of an effort to integrate Intel ICC into Travis CI for the Boost Project.  Nick Thompson made the original request and we were able to secure a license to use.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-12-21 22:16:00 UTC  
> Url: https://github.com/boostorg/system/issues/29#issuecomment-449511852  

Note this may be a result of pre-C++17 support.  I am going to see if it is limited to that, and cap the CI build so it only does C++11, C++14.  If this is the case, it's likely not our issue, but if someone could take a look and confirm that would be great.

---

## Comment 3

> Username: jeking3  
> Created at: 2018-12-21 22:20:30 UTC  
> Url: https://github.com/boostorg/system/issues/29#issuecomment-449512662  

Scratch that, I think it's because libstdc++ is too old, fixing my work and will report...

---

## Comment 4

> Username: jeking3  
> Created at: 2018-12-21 22:36:17 UTC  
> Url: https://github.com/boostorg/system/issues/29#issuecomment-449515284  

Yep that was it, I forgot to update libstdc++ - issue resolved.

---

## Comment 5

> Username: pdimov  
> Created at: 2018-12-21 23:43:05 UTC  
> Url: https://github.com/boostorg/system/issues/29#issuecomment-449524691  

I don't see how this could be caused by libstdc++. For one, Boost.System's Travis does test the default libstdc++ 4.8. For another, the error looks like the result of applying C++11's constexpr to the member function `assign` (in C++11 `constexpr` implies `const`), but it shouldn't, because `assign` should only be constexpr on C++14 and above. (https://github.com/boostorg/system/blob/develop/include/boost/system/detail/config.hpp#L32)

---

## Comment 6

> Username: jeffhammond  
> Created at: 2018-12-21 23:51:00 UTC  
> Url: https://github.com/boostorg/system/issues/29#issuecomment-449525626  

If your default GCC is too old, you can tell ICC to use a newer one as follows.  This is convenient if you don't want to override the default GCC from taking precedence in your path.  
```  
-gcc-name=<name>  
          name and location of gcc if not where expected  
-gxx-name=<name>  
          name and location of g++ if not where expected  
```

---

## Comment 7

> Username: pdimov  
> Created at: 2018-12-21 23:59:38 UTC  
> Url: https://github.com/boostorg/system/issues/29#issuecomment-449526566  

I suppose it's possible that ICC sees libstdc++ 4.8 and turns on some sort of compatibility mode, as libstdc++ 4.8 is C++11 and doesn't work under C++14 (it [libstdc++ 4.8] assumes that constexpr implies const.)

---

## Comment 8

> Username: jeffhammond  
> Created at: 2018-12-22 00:16:36 UTC  
> Url: https://github.com/boostorg/system/issues/29#issuecomment-449528856  

I would have to test to be sure, but I think there's a good chance that this is happening.  ICC runs the associated GCC compiler as part of determining what it can/should support.  
  
Was `icpc -std=c++17` used here?

---

## Comment 9

> Username: jeking3  
> Created at: 2018-12-22 00:34:56 UTC  
> Url: https://github.com/boostorg/system/issues/29#issuecomment-449530593  

The issue was specifically that I forgot to update the libstdc++ in the build job so it remained at an older version (4.8).  Once I revved it to 7.3, all was well.
