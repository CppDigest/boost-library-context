# #221 - Ship prebuilt libraries [Closed]

> Username: KOLANICH  
> Created at: 2018-11-21 09:02:14 UTC  
> Updated at: 2019-06-26 15:09:58 UTC  
> Closed at: 2019-06-26 15:09:57 UTC  
> Url: https://github.com/boostorg/boost/issues/221  

It is possible to setup appveyor to build binaries on every commit and store the artifacts.  
  
We need it to build the libs for all the major compilers and runtimes: MinGW-w64, VS. CLang is somehow compatible to both.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2018-11-28 15:49:58 UTC  
> Url: https://github.com/boostorg/boost/issues/221#issuecomment-442495997  

You should consider using a package manager instead or set up your own CI to build what you need.

---

## Comment 2

> Username: KOLANICH  
> Created at: 2018-11-28 16:34:40 UTC  
> Url: https://github.com/boostorg/boost/issues/221#issuecomment-442513302  

>You should consider using a package manager instead  
  
it only applies to Linux.   
  
>or set up your own CI to build what you need.  
  
I don't want to build Boost from source, I want the project ship official binaries. Currently it ships only binaries for proprietary MS toolchain. I need the binaries for MinGW-w64. Like the ones provided by Fedora (yes, Fedora provides Windows DLLs and static libs), but the official ones (Fedora can stop doing this any moment).

---

## Comment 3

> Username: mjcaisse  
> Created at: 2019-06-26 15:09:57 UTC  
> Url: https://github.com/boostorg/boost/issues/221#issuecomment-505918269  

Boost currently has no plan to ship or maintain binaries. The number of problems with compatibility from compiler and linker flags alone makes this unfeasible for Boost.  
  
Boost provides official releases of source code.
