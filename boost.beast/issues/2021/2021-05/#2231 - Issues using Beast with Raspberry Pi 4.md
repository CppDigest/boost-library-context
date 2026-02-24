# #2231 - Issues using Beast with Raspberry Pi 4 [Closed]

> Username: AnthonySabbagh  
> Created at: 2021-05-12 16:15:12 UTC  
> Updated at: 2022-06-16 14:52:56 UTC  
> Closed at: 2022-06-16 14:52:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2231  

I am working on a project that is meant to be run on a Raspberry Pi 4. I can compile and run the project on my Ubuntu system. However, after adding the websocket part of my project using Beast, I can no longer compile on the Raspberry Pi, or run a cross-compiled binary that I compile on my Ubuntu machine. I will describe the issue that occurs when I try to compile on the Raspberry Pi 4 below.  
  
As for cross-compiling, I have set up a cross compiler mostly following this guide. I am able to cross-compile the project, but after adding the websocket feature, I get this error when running the executable: ./handover_websocket: /usr/lib/arm-linux-gnueabihf/libstdc++.so.6: version `GLIBCXX_3.4.26' not found (required by ./handover_websocket)  
  
The Pi only has  GLIBCXX_3.4.25 available. Does Beast require 3.4.26, or have I set up something wrong in my cross-compiler?  
  
### Version of Beast  
  
306  
  
### Steps necessary to reproduce the problem  
  
I am trying to create a callback as follows:  
on_connect_callback_ = boost::beast::bind_front_handler(  
            &WebSocket::on_connect,  
            shared_from_base<WebSocket>());  
  
The method shared_from_base is defined in the parent class of WebSocket as follows:  
std::shared_ptr<Derived> shared_from_base()  
{  
    return std::static_pointer_cast<Derived>(shared_from_this());  
}  
  
The compiling error is the following:  
In file included from include/boost/beast/core/bind_handler.hpp:14,  
                 from include/boost/beast/core/async_base.hpp:14,  
                 from include/boost/beast/core.hpp:15,  
                 from src/WebSocketSession.h:12,  
                 from src/WebSocket.h:19,  
                 from src/WebSocket.cpp:13:  
include/boost/beast/core/detail/bind_handler.hpp: In instantiation of ‘boost::beast::detail::bind_front_wrapper<Handler, Args>::bind_front_wrapper(Handler_&&, Args_&& ...) [with Handler_ = boost::beast::detail::bind_front_wrapper<void (WebSocket::*)(), std::shared_ptr<WebSocket> >&; Args_ = {}; Handler = void (WebSocket::*)(); Args = {std::shared_ptr<WebSocket>}]’:  
/usr/include/c++/8/bits/std_function.h:173:6:   required from ‘static void std::_Function_base::_Base_manager<_Functor>::_M_clone(std::_Any_data&, const std::_Any_data&, std::false_type) [with _Functor = boost::beast::detail::bind_front_wrapper<void (WebSocket::*)(), std::shared_ptr<WebSocket> >; std::false_type = std::integral_constant<bool, false>]’  
/usr/include/c++/8/bits/std_function.h:208:16:   required from ‘static bool std::_Function_base::_Base_manager<_Functor>::_M_manager(std::_Any_data&, const std::_Any_data&, std::_Manager_operation) [with _Functor = boost::beast::detail::bind_front_wrapper<void (WebSocket::*)(), std::shared_ptr<WebSocket> >]’  
/usr/include/c++/8/bits/std_function.h:676:19:   required from ‘std::function<_Res(_ArgTypes ...)>::function(_Functor) [with _Functor = boost::beast::detail::bind_front_wrapper<void (WebSocket::*)(), std::shared_ptr<WebSocket> >; <template-parameter-2-2> = void; <template-parameter-2-3> = void; _Res = void; _ArgTypes = {}]’  
/usr/include/c++/8/bits/std_function.h:524:4:   required from ‘std::function<_Res(_ArgTypes ...)>::_Requires<std::function<_Res(_ArgTypes ...)>::_Callable<typename std::decay<_U1>::type>, std::function<_Res(_ArgTypes ...)>&> std::function<_Res(_ArgTypes ...)>::operator=(_Functor&&) [with _Functor = boost::beast::detail::bind_front_wrapper<void (WebSocket::*)(), std::shared_ptr<WebSocket> >; _Res = void; _ArgTypes = {}; std::function<_Res(_ArgTypes ...)>::_Requires<std::function<_Res(_ArgTypes ...)>::_Callable<typename std::decay<_U1>::type>, std::function<_Res(_ArgTypes ...)>&> = std::function<void()>&]’  
src/WebSocket.cpp:40:42:   required from here  
include/boost/beast/core/detail/bind_handler.hpp:254:45: error: cannot convert ‘boost::beast::detail::bind_front_wrapper<void (WebSocket::*)(), std::shared_ptr<WebSocket> >’ to ‘void (WebSocket::*)()’ in initialization  
         , args_(std::forward<Args_>(args)...)  
  
### All relevant compiler information  
Output of g++ -v:  
$ g++ -v  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/arm-linux-gnueabihf/8/lto-wrapper  
Target: arm-linux-gnueabihf  
Configured with: ../src/configure -v --with-pkgversion='Raspbian 8.3.0-6+rpi1' --with-bugurl=file:///usr/share/doc/gcc-8/README.Bugs --enable-languages=c,ada,c++,go,d,fortran,objc,obj-c++ --prefix=/usr --with-gcc-major-version-only --program-suffix=-8 --program-prefix=arm-linux-gnueabihf- --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --enable-bootstrap --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-libitm --disable-libquadmath --disable-libquadmath-support --enable-plugin --with-system-zlib --with-target-system-zlib --enable-objc-gc=auto --enable-multiarch --disable-sjlj-exceptions --with-arch=armv6 --with-fpu=vfp --with-float=hard --disable-werror --enable-checking=release --build=arm-linux-gnueabihf --host=arm-linux-gnueabihf --target=arm-linux-gnueabihf  
Thread model: posix  
gcc version 8.3.0 (Raspbian 8.3.0-6+rpi1)  
  
Compiler flags in use:  
-pedantic-errors -Wall -Wextra -Werror -std=c++17 -pthread

---

## Comment 1

> Username: AnthonySabbagh  
> Created at: 2021-05-12 17:09:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-839948464  

For the cross-compiler I was using gcc 10.1.0, but if I use 8.3.0, I get the same error. I will try cross compiling with 9.3.0, but not sure why it acts so differently with different compiler versions.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-05-12 17:37:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-839967629  

The error is in your file:  
> from src/WebSocket.cpp:13:

---

## Comment 3

> Username: AnthonySabbagh  
> Created at: 2021-05-12 19:13:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-840032707  

Yes it seems that my code only works in gcc 9 and 10, but not 8. I'm still wondering why the cross compiled code won't run on the pi. Do you know any reason why including beast in the project would make it require GLIBCXX_3.4.26?

---

## Comment 4

> Username: madmongo1  
> Created at: 2021-05-12 19:27:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-840040025  

Nothing springs to mind, but for us this is a corner case. If anything comes up in your investigations I'd like to hear about it so we can take any necessary action.  
  
We do test against GCC8 as you can see from our CI:  
https://github.com/boostorg/beast/runs/2381774539?check_suite_focus=true  
  
But of course not all GCC8s are equal.

---

## Comment 5

> Username: AnthonySabbagh  
> Created at: 2021-05-12 21:03:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-840094973  

After more investigation it seems that the issue is that I am trying to create a callback by assigning the return value of `boost::beast::bind_front_handler` to a variable of type `std::function<void()>`. This works in GCC9 and 10, but not GCC8. Is this expected? Is there a different type I should use instead of `std::function<void()>` to store the callback?

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-05-12 21:43:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-840115937  

Std function is not a good completion handler as it requires that the stored function object is copyable. Completion handlers may be move-only.  
 It sure if that’s the issue though

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-05-12 21:43:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-840116310  

There is a library called fu2 which provides polymorphic move only function objects

---

## Comment 8

> Username: AnthonySabbagh  
> Created at: 2021-05-13 14:14:10 UTC  
> Updated at: 2021-05-13 14:15:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-840590570  

Thanks, I will try using that instead. Also I found another issue cross compiling for raspbery pi. I thought I would write it here in case anyone else needs it later. I got the error that SSIZE_MAX was not declared. That's because for some reason in my gcc 8.3.0 cross compiler, lib/gcc/arm-linux-gnueabihf/8.3.0/include-fixed/limits.h did not have the following lines:  
`#ifdef  __USE_POSIX`  
`* POSIX adds things to <limits.h>.  */`  
`# include <bits/posix1_lim.h>`  
`#endif`  
So some beast files were trying to use SSIZE_MAX with __USE_POSIX defined, but bits/posix1_lim.h was not being included, which is where SSIZE_MAX is defined. So adding those lines to the limits.h file solved the issue. Or including bits/posix1_lim.h directly where it is needed works, but that seemed messier.

---

## Comment 9

> Username: AnthonySabbagh  
> Created at: 2021-05-13 14:21:47 UTC  
> Updated at: 2021-05-13 14:22:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-840595038  

Also, cross compiling with GCC 8.3.0 instead of 9 or 10 removes the error of `/handover_websocket: /usr/lib/arm-linux-gnueabihf/libstdc++.so.6: version 'GLIBCXX_3.4.26' not found (required by ./handover_websocket)` and the executable will actually run. Not sure if there is a way to cross compile with later gcc versions, but this works.

---

## Comment 10

> Username: madmongo1  
> Created at: 2021-05-13 18:32:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-840749726  

Thanks for the feedback @AnthonySabbagh   
I did a little digging.  
SSIZE_MAX is used in the implementation file `file_posix.ipp` which is intended only for unclusion in posix systems.  
The symbol `SSIZE_MAX` is defined by POSIX but not by the C or C++ standards.  
I am informed that there is no strict requirement that `<limits.h>` must bring in the posix definitions so it may be that beast is being over eager here.  
I'm going to raise this as an issue and investigate a fix.  
  
Maybe along the lines of:  
```  
namespace boost {  
namespace beast {  
using ssize_t = std::make_signed<std::size_t>  
}  
}  
```  
  
Thanks for raising this.

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-05-13 18:37:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-840752213  

https://github.com/boostorg/beast/issues/2233

---

## Comment 12

> Username: stale[bot]  
> Created at: 2022-01-09 04:16:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-1008226714  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 13

> Username: vinniefalco  
> Created at: 2022-06-16 14:52:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2231#issuecomment-1157753059  

it looks like this is resolved
