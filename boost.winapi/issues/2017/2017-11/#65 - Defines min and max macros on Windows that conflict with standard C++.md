# #65 - Defines min and max macros on Windows that conflict with standard C++ [Closed]

> Username: peetw  
> Created at: 2017-11-29 20:33:23 UTC  
> Updated at: 2017-11-30 10:29:58 UTC  
> Closed at: 2017-11-29 21:04:18 UTC  
> Url: https://github.com/boostorg/winapi/issues/65  

I was recently using the new [stacktrace](https://github.com/boostorg/stacktrace) module on Windows and discovered that it ([eventually](https://github.com/boostorg/winapi/blob/develop/include/boost/winapi/basic_types.hpp#L21)) ends up including `windows.h`. Unfortunately this header defines `min` and `max` macros that conflict with the standard C++ ones.  
  
To work around this I currently have to define `NOMINMAX` (or undef `BOOST_USE_WINDOWS_H`) in my code. However, as discussed in a [bug report](https://svn.boost.org/trac10/ticket/3901) for the ASIO module, this breaks encapsulation (I shouldn't have to care about the internal implementation of the stacktrace module).  
  
This issue was solved for the ASIO module by defining `NOMINMAX` by default (see [here](https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/config.hpp#L905)). However, when I suggested a similar fix for the stacktrace module (see boostorg/stacktrace/issues/37), it was suggested that it may be better to fix this issue at "source".

---

## Comment 1

> Username: Lastique  
> Created at: 2017-11-29 21:04:18 UTC  
> Url: https://github.com/boostorg/winapi/issues/65#issuecomment-347995639  

`NOMINMAX` is a user's config macro. It should be defined by the user because he may have included `windows.h` on his own before or after Boost headers. Also, the user may actually need those macros (I think, they were needed in some Windows or MFC headers), and it's generally not Boost's job to deny him that.  
  
The right way to deal with these macros is to protect Boost code from them. There are a number of techniques that I will outline in https://github.com/boostorg/stacktrace/issues/37.

---

## Comment 2

> Username: peetw  
> Created at: 2017-11-29 21:24:41 UTC  
> Updated at: 2017-11-29 21:25:16 UTC  
> Url: https://github.com/boostorg/winapi/issues/65#issuecomment-348000980  

I have to disagree here - including a boost module that uses winapi should not break any unrelated user code using `std::min`/`std::max` from standard C++ headers (`<limits>` and `<algorithm>`). I know users can work around it, but surely the default should be for winapi to define `NOMINMAX`, and if users require the redefined `min`/`max` macros, then they can specify some macro (e.g. `BOOST_WINAPI_NO_NOMINMAX`)? This would be similar to the [approach](https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/config.hpp#L905) taken by the ASIO module.  
  
Something like:  
  
    #if defined( BOOST_USE_WINDOWS_H )  
    # if !defined(BOOST_WINAPI_NO_NOMINMAX)  
    #  if !defined(NOMINMAX)  
    #   define NOMINMAX 1  
    #  endif  
    # endif  
    # include <windows.h>  
    #else ...

---

## Comment 3

> Username: Lastique  
> Created at: 2017-11-29 21:35:07 UTC  
> Url: https://github.com/boostorg/winapi/issues/65#issuecomment-348003839  

Again, that doesn't work if `windows.h` is already included. Boost code should be prepared for that. Boost.ASIO approach, I think, is incorrect.  
  
If including `windows.h` breaks standard library headers then please report it to Microsoft. They brought this on themselves.

---

## Comment 4

> Username: peetw  
> Created at: 2017-11-29 21:50:24 UTC  
> Url: https://github.com/boostorg/winapi/issues/65#issuecomment-348008179  

If a user has already included `windows.h` without defining `NOMINMAX`, then the user has already made that choice and so it doesn't matter. However, if a project is not already using `windows.h` and decides to use a boost module that uses winapi, they are now using `windows.h` without necessarily realising. This is particularly problematic since the resulting compiler errors (due to the definition of `min`/`max` macros) are pretty cryptic and it is not clear that the boost module is the cause since users cannot be expected to know about the **private** implementation of the boost module...  
  
This issue is already well known (see [here](https://support.microsoft.com/en-gb/help/143208/prb-using-stl-in-windows-program-can-cause-min-max-conflicts) and [here](https://stackoverflow.com/a/4914108/3628232), for example) and I would suspect that most users including `windows.h` would be defining `NOMINMAX`.  
  
I'm not sure why having winapi define `NOMINMAX` by default (with the ability for users to opt-out if they need that functionality) is so bad, given that it would prevent many errors? As it stands, it is currently leaking implementation details into the including projects...

---

## Comment 5

> Username: peetw  
> Created at: 2017-11-30 06:58:18 UTC  
> Url: https://github.com/boostorg/winapi/issues/65#issuecomment-348099850  

Also, I'm not sure that I would call the approach taken by the ASIO module "incorrect" - it's been in use for nearly 8 years now and no-one has complained ;)

---

## Comment 6

> Username: MarcelRaad  
> Created at: 2017-11-30 08:22:03 UTC  
> Updated at: 2017-11-30 08:22:17 UTC  
> Url: https://github.com/boostorg/winapi/issues/65#issuecomment-348115581  

@peetw As a user, I just treat Windows SDK defines like C++ standard library defines: never define them in source code, but always globally as compiler options (or in VS project files / property sheets) so that external libraries can include what they want without breaking my defines.  
  
IMO external libraries defining things for me I potentially do not want is more evil than not defining things as I can't undo that. And the Windows SDK 7.1 itself (used for targeting Windows XP) is not fully compatible with `NOMINMAX` without user workarounds at least for GDI+, so defining it might lead to compilation errors too.  
  
Also note that `windows.h` is only included if `BOOST_USE_WINDOWS_H` is manually defined, in which case this is probably expected.

---

## Comment 7

> Username: peetw  
> Created at: 2017-11-30 08:53:38 UTC  
> Updated at: 2017-11-30 09:04:21 UTC  
> Url: https://github.com/boostorg/winapi/issues/65#issuecomment-348122697  

"Also note that `windows.h` is only included if `BOOST_USE_WINDOWS_H` is manually defined, in which case this is probably expected."  
  
This is not strictly true - `BOOST_USE_WINDOWS_H` appears to be defined by default when compiling on Windows (at least when using the boost NuGet package).  
  
Making winapi temporarily define `NOMINMAX` (i.e. undefine it again immediately after the `windows.h` include directive) by default would avoid breaking users' code in the majority of use cases. In the rare cases where users actually require the `min`/`max` macros from `windows.h` (and have not included it in their own code...), they could opt-out of this behaviour by defining `BOOST_WINAPI_NO_NOMINMAX` (as done in the ASIO module). Best of both worlds, no? Having boost modules define `min`/`max` macros **by default** at the moment seems a bit odd since it requires users to have knowledge about the private implementation of the module.

---

## Comment 8

> Username: MarcelRaad  
> Created at: 2017-11-30 08:59:20 UTC  
> Url: https://github.com/boostorg/winapi/issues/65#issuecomment-348124005  

> `BOOST_USE_WINDOWS_H` appears to be defined by default when compiling on Windows (at least when using the boost NuGet package).  
  
Ah, that's probably the issue then. It's not defined by default in any Boost header as far as I can tell. I had to define it manually to be able to build with clang on Windows.

---

## Comment 9

> Username: peetw  
> Created at: 2017-11-30 10:29:58 UTC  
> Url: https://github.com/boostorg/winapi/issues/65#issuecomment-348147763  

Hmm, my mistake - it looks like `BOOST_USE_WINDOWS_H` is not actually defined by default and that the problem does indeed actually lie with the stacktrace module and not the winapi module - my apologies!  
  
I have updated [the original issue](https://github.com/boostorg/stacktrace/issues/37#issuecomment-348147710) with the new info if you are interested :)
