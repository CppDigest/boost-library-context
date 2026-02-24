# #13 - Pull coroutine does not rethrow an exception if it is thrown on the first entry [Closed]

> Username: ztlpn  
> Created at: 2017-08-08 18:54:48 UTC  
> Updated at: 2017-08-08 20:03:27 UTC  
> Closed at: 2017-08-08 19:37:03 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/13  

Here is the test program: https://gist.github.com/ztlpn/77a0ff1c0537f5ce8f81227c511b028b  
  
I expect that the exception will be rethrown and the program will print `Exception: Error`, but it prints nothing. If the `yield()` statement is uncommented, everything is as expected.  
  
I am testing with coroutine2 library from boost 1.64.0.

---

## Comment 1

> Username: olk  
> Created at: 2017-08-08 19:37:03 UTC  
> Updated at: 2017-08-08 19:37:14 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/13#issuecomment-321059470  

ty, rethrow_exception() was missing in the ctor of pull-coroutine ... fixed in branch develop (unfortunately too late for boost-1.65).

---

## Comment 2

> Username: ztlpn  
> Created at: 2017-08-08 20:03:27 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/13#issuecomment-321065855  

Ok, thanks for the quick fix!
