# #328 - boost 1.87 does not have bjam options 'numa' or 'context-impl' [Closed]

> Username: bgemmill  
> Created at: 2024-12-18 21:43:51 UTC  
> Updated at: 2025-02-21 18:18:00 UTC  
> Closed at: 2025-02-21 14:30:35 UTC  
> Url: https://github.com/boostorg/fiber/issues/328  

When compiling boost with fiber, version 1.86 would take the bjam options `numa=on` and `context-impl=ucontext` and build appropriately.  
  
With boost 1.87, I see:  
`error: Invalid property '<numa>on': unknown feature 'numa'.`  
and  
`error: unknown feature "<context-impl>"`  
  
The [documentation](https://www.boost.org/doc/libs/1_87_0/libs/fiber/doc/html/fiber/overview/implementations__fcontext_t__ucontext_t_and_winfiber.html) still mentions that this is the way to pass these options in; has the method changed?

---

## Comment 1

> Username: praetorian20  
> Created at: 2025-02-20 19:56:41 UTC  
> Url: https://github.com/boostorg/fiber/issues/328#issuecomment-2672540128  

@olk could you kindly take a look at this? Is the `numa=on` b2 option no longer supported? This failure is preventing 1.87 from being added to conan-center-index, [conan-io/conan-center-index#26185](https://github.com/conan-io/conan-center-index/pull/26185)

---

## Comment 2

> Username: bgemmill  
> Created at: 2025-02-20 21:19:24 UTC  
> Url: https://github.com/boostorg/fiber/issues/328#issuecomment-2672707644  

@praetorian20 the removal of those two b2 options led me to have a look at the [boost cmake module](https://github.com/boostorg/cmake) where libboost_fiber_numa appears to be built by default, and ucontext can be controlled at cmake configure time with `-DBOOST_CONTEXT_IMPLEMENTATION=`

---

## Comment 3

> Username: praetorian20  
> Created at: 2025-02-20 23:27:15 UTC  
> Url: https://github.com/boostorg/fiber/issues/328#issuecomment-2672937881  

@bgemmill since CMake is not the official build system, I don't know whether we can rely on that or not. I [compared](https://github.com/boostorg/cmake/compare/boost-1.86.0...boost-1.87.0) boost cmake between the 1.86 and 1.87 tags, there's no modification of fiber build conditions.  
  
Similar [comparison](https://github.com/boostorg/fiber/compare/boost-1.86.0...boost-1.87.0) of fiber shows Jamfile refactoring, maybe this was unintentionally introduced? I don't understand Jamfile syntax so I can't tell which change is causing this.

---

## Comment 4

> Username: olk  
> Created at: 2025-02-21 14:30:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/328#issuecomment-2674703196  

should be fixed with commit _238487b fix property numa_

---

## Comment 5

> Username: bgemmill  
> Created at: 2025-02-21 15:30:17 UTC  
> Url: https://github.com/boostorg/fiber/issues/328#issuecomment-2674858657  

@olk Can you please also fix the `context-impl` option while you're in there?

---

## Comment 6

> Username: olk  
> Created at: 2025-02-21 15:58:32 UTC  
> Url: https://github.com/boostorg/fiber/issues/328#issuecomment-2674924419  

`b2 context-impl=ucontext` works for me - please not that you have to build boost.context too

---

## Comment 7

> Username: bgemmill  
> Created at: 2025-02-21 18:17:58 UTC  
> Url: https://github.com/boostorg/fiber/issues/328#issuecomment-2675243886  

Thanks for checking, if it works for you my b2 instance was probably choking on not recognizing `numa` and the `context-impl` error was noise.  
  
I'm building with cmake at the moment, I'll check b2 again when the next boost lands with your fix.
