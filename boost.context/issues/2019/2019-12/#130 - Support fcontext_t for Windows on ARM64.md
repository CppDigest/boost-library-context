# #130 - Support fcontext_t for Windows on ARM64 [Closed]

> Username: yurybura  
> Created at: 2019-12-15 18:54:04 UTC  
> Updated at: 2021-07-13 19:06:14 UTC  
> Closed at: 2021-07-13 19:06:14 UTC  
> Url: https://github.com/boostorg/context/issues/130  



---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 08:59:47 UTC  
> Url: https://github.com/boostorg/context/issues/130#issuecomment-618893036  

you are welcome to provide the necessary assembler code

---

## Comment 2

> Username: janisozaur  
> Created at: 2020-05-17 15:33:11 UTC  
> Url: https://github.com/boostorg/context/issues/130#issuecomment-629816554  

I've provided necessary fix across https://github.com/boostorg/context/pull/122 and https://github.com/boostorg/build/pull/502, there's still a patch needed to coroutine, I think it's the final piece. I had it mostly working in naive version that didn't pass one of the tests (I think it was the fcontext one?). Sadly, I may have lost the patch, but will try restoring it and publish it again. While the test failed, the patch still unblocked other work as I believe it could complete boost installation with vcpkg.

---

## Comment 3

> Username: olk  
> Created at: 2021-07-13 19:06:14 UTC  
> Url: https://github.com/boostorg/context/issues/130#issuecomment-879329983  

closed because no progress/no assembler provided
