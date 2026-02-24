# #134 - Make make_fcontext jump_fcontext as public api [Closed]

> Username: Klayflash  
> Created at: 2020-03-07 05:50:03 UTC  
> Updated at: 2020-04-24 09:44:31 UTC  
> Closed at: 2020-04-24 08:59:06 UTC  
> Url: https://github.com/boostorg/context/issues/134  

Hello.  
  
Currently I use make_fcontext and jump_fcontext as replacement of windows api: CreateFiberEx, SwitchToFiber. It is required to port old code. Please make the functions public.  
  
I need only context switching capabilities. I think that this ability should be exported this library separately.  
  
Also it will be harder to use private functions after new C++ standard modules will arrive.

---

## Comment 1

> Username: olk  
> Created at: 2020-04-24 08:59:06 UTC  
> Url: https://github.com/boostorg/context/issues/134#issuecomment-618892693  

It is not a good idea to make these functions part of the public API.  
The public API prevents accidental resuming an already running fiber, manages lifetime of the stack etc.

---

## Comment 2

> Username: Klayflash  
> Created at: 2020-04-24 09:15:27 UTC  
> Url: https://github.com/boostorg/context/issues/134#issuecomment-618900381  

How to use boost library to port old code from CreateFiberEx, SwitchToFiber and GetCurrentFiber?  
Task: replace winapi code to make it portable.

---

## Comment 3

> Username: olk  
> Created at: 2020-04-24 09:20:02 UTC  
> Url: https://github.com/boostorg/context/issues/134#issuecomment-618902499  

use boost::context:.fiber instead - of course you have to adopt your code

---

## Comment 4

> Username: Klayflash  
> Created at: 2020-04-24 09:25:14 UTC  
> Url: https://github.com/boostorg/context/issues/134#issuecomment-618904846  

boost::context:.fiber does not provide GetCurrentFiber. So code adaptation will be costly.

---

## Comment 5

> Username: olk  
> Created at: 2020-04-24 09:33:46 UTC  
> Url: https://github.com/boostorg/context/issues/134#issuecomment-618908745  

GetCurrentFiber() is dangerous ... you could try to destroy the current fiber by accident  
boost.context's fiber tries to prevent such problems

---

## Comment 6

> Username: Klayflash  
> Created at: 2020-04-24 09:44:31 UTC  
> Url: https://github.com/boostorg/context/issues/134#issuecomment-618913433  

In my case GetCurrentFiber is not dangerous because of it is already debugged and long time running on end user. I just want make my code more portable by replacing functions calls.  
  
I sure this applied is not only for me. So this functionality will be useful for other people.
