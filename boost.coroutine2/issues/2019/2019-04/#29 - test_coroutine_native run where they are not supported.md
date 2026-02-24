# #29 - test_coroutine_native run where they are not supported [Closed]

> Username: Kojoley  
> Created at: 2019-04-05 21:51:45 UTC  
> Updated at: 2023-10-01 14:58:52 UTC  
> Closed at: 2023-10-01 14:58:52 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/29  

The test requirements does not include requirements of context fibers. On 32bit MinGW `thread_local` is banned in boost because `__cxa_thread_atexit` crashes with them, resulting in not building fibers (the `cxx11_thread_local` requirement), but the test does not have that requirement and fails at linking. It either should be fixed in context by providing pseudo fiber library that will have usage requirements or just mirroring fiber build requirements on tests here.

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 09:15:26 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/29#issuecomment-618900363  

could you provide a patch please

---

## Comment 2

> Username: Kojoley  
> Created at: 2020-04-24 14:57:32 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/29#issuecomment-619062492  

I do not have a patch, sorry
