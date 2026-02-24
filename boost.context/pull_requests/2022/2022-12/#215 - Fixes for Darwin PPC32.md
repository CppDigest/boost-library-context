# #215 Fixes for Darwin PPC32 [Merged]

> Username: evanmiller  
> Created at: 2022-12-27 13:48:40 UTC  
> Updated at: 2023-05-15 07:36:15 UTC  
> Merged at: 2022-12-27 19:49:27 UTC  
> Closed at: 2022-12-27 19:49:27 UTC  
> Url: https://github.com/boostorg/context/pull/215  

This PR consolidates some of @kernigh's work over in #211. It successfully compiles and runs the Fibonacci test, without a crash, on PPC32 hardware running Darwin 8:  
  
```  
$ ./a.out  
0 1 1 2 3 5 8 13 21 34  
main: done  
$ uname -r  
8.11.0  
```  
  
For `make_fcontext`, use the diff provided here:  
  
https://github.com/boostorg/context/issues/211#issuecomment-1328154669  
  
For `ontop_context`, adapt the Linux PPC32 fixes from here:  
  
https://github.com/boostorg/context/commit/df8fb6b5289492914dd2af479e40e9737399fd21

---

## Comment 1

> Username: barracuda156  
> Created_at: 2022-12-27 15:40:13 UTC  
> Url: https://github.com/boostorg/context/pull/215#issuecomment-1365992314  

@evanmiller Do you think we should first ensure that `fiber` does not fail with these changes? Given that is was building before with not fully fixed `context`, it presumably should build with the properly fixed one.

---

## Comment 2

> Username: evanmiller  
> Created_at: 2022-12-27 15:51:04 UTC  
> Url: https://github.com/boostorg/context/pull/215#issuecomment-1366003825  

> @evanmiller Do you think we should first ensure that `fiber` does not fail with these changes? Given that is was building before with not fully fixed `context`, it presumably should build with the properly fixed one.  
  
@barracuda156 Which compilers / Boost versions were successfully building Fiber on PPC? I encountered an issue with GCC7 / Boost 1.81 but I can't comment on any other combinations.

---

## Comment 3

> Username: barracuda156  
> Created_at: 2022-12-27 16:15:02 UTC  
> Url: https://github.com/boostorg/context/pull/215#issuecomment-1366019259  

> > @evanmiller Do you think we should first ensure that `fiber` does not fail with these changes? Given that is was building before with not fully fixed `context`, it presumably should build with the properly fixed one.  
>   
> @barracuda156 Which compilers / Boost versions were successfully building Fiber on PPC? I encountered an issue with GCC7 / Boost 1.81 but I can't comment on any other combinations.  
  
Well, everything was building with my earlier minimal fixes to `context` (which are not sufficient, as we know now).

---

## Comment 4

> Username: evanmiller  
> Created_at: 2022-12-27 16:44:32 UTC  
> Url: https://github.com/boostorg/context/pull/215#issuecomment-1366037202  

@barracuda156 If you were able to build everything before, why don't you try building Fiber with these proposed changes and report back?

---

## Comment 5

> Username: barracuda156  
> Created_at: 2022-12-27 17:39:52 UTC  
> Url: https://github.com/boostorg/context/pull/215#issuecomment-1366069107  

> @barracuda156 If you were able to build everything before, why don't you try building Fiber with these proposed changes and report back?  
  
@evanmiller I will do it soonish.

---

## Comment 6

> Username: olk  
> Created_at: 2022-12-27 19:49:33 UTC  
> Url: https://github.com/boostorg/context/pull/215#issuecomment-1366135569  

ty

---

## Comment 7

> Username: barracuda156  
> Created_at: 2022-12-27 19:51:50 UTC  
> Url: https://github.com/boostorg/context/pull/215#issuecomment-1366136775  

Now we have `ppc64` left to fix. And possibly `fiber` for both.

---

## Comment 8

> Username: barracuda156  
> Created_at: 2023-05-15 07:36:15 UTC  
> Url: https://github.com/boostorg/context/pull/215#issuecomment-1547337857  

@evanmiller Could you remind, what prevented us from fixing `ppc64` along?

---
