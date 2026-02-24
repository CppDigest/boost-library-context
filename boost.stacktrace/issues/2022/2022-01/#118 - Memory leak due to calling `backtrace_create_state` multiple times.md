# #118 - Memory leak due to calling `backtrace_create_state` multiple times [Closed]

> Username: Cogitri  
> Created at: 2022-01-12 09:11:04 UTC  
> Updated at: 2024-02-23 09:39:33 UTC  
> Closed at: 2024-02-23 09:39:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/118  

Hello,  
  
currently boost-stacktrace with the `backtrace` backend will call [backtrace_create_state](https://github.com/boostorg/stacktrace/blob/08d720adbd01c0187af256eb491cffece7e87857/include/boost/stacktrace/detail/libbacktrace_impls.hpp#L104) once for each thread. This creates a memory leak since each time a new thread is created, a new `backtrace_state` is allocated but it's never freed (and can't be since there's no `backtrace_destroy_state`).  See e.g. https://patchwork.ozlabs.org/project/gcc/patch/3defaafeedc9fa6b42e61401d4c8c84e@starynkevitch.net/#1625428 for more discussion on this.

---

## Comment 1

> Username: apolukhin  
> Created at: 2022-09-01 15:38:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/118#issuecomment-1234454758  

Unfortunately, it's not a bug but a feature:  
  
https://github.com/boostorg/stacktrace/blob/08d720adbd01c0187af256eb491cffece7e87857/include/boost/stacktrace/detail/libbacktrace_impls.hpp#L74-L85  
  
There's some issue either with the packaged versions of libbacktrace, or some issue in the libbacktrace with some compiler flags that I failed to localize https://gcc.gnu.org/bugzilla//show_bug.cgi?id=87653  
  
I have to add more docs and references into the sources and provide a macro to force single instance for those' who wish to take the risk

---

## Comment 2

> Username: pergraa-celonis  
> Created at: 2022-11-15 10:59:22 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/118#issuecomment-1315142952  

Should that be `static` and not `ststic` here?  
https://github.com/boostorg/stacktrace/commit/308b7f6b087a302795d6a189373f254f610a826f#diff-1f3dccb3928ad7b52e4c986270cb619b663197283b764c2fb68256d285f21a3fR124

---

## Comment 3

> Username: apolukhin  
> Created at: 2023-01-18 14:15:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/118#issuecomment-1387142887  

Oops, thanks!
