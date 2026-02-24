# #14 - Crash while stack unwinding, if compiled by Visual Studio 2015 Upd.3 without / GL flag [Closed]

> Username: DrKelwin  
> Created at: 2017-09-14 16:11:30 UTC  
> Updated at: 2018-06-17 12:20:48 UTC  
> Closed at: 2017-10-26 06:35:02 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/14  

[BoostCoroTest.zip](https://github.com/boostorg/coroutine2/files/1303646/BoostCoroTest.zip)  
I've attached configured sample which crashes (*.sln and source file). Can be reproduced if this solution compiled on Release & x86 configuration. /O2 and /Ob2 are enabled, all other options are disabled. If /GL (in VS it calls: "Whole Program Optimization") is enabled all works fine.  
  
I used Boost.Coroutine2 from boost 1.65.1.

---

## Comment 1

> Username: olk  
> Created at: 2017-09-14 17:50:48 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/14#issuecomment-329558989  

does it happen with context-impl=winfib (b2 command line property; uses Windows Fibers instead of assembler implementation) too?

---

## Comment 2

> Username: DrKelwin  
> Created at: 2017-09-25 14:08:01 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/14#issuecomment-331892546  

Great! Thanks! context-impl=winfib helped. But it works only if link=static. If I changed to link=shared then I see errors like   
  
"boost/context/continuation_winfib.hpp(66): error C2492: 'boost::context::detail::activation_record::current_rec': data with thread storage duration may not have dll interface".  
  
Do you have any ideas how can I fix it? Maybe some preprocessor definitions will help or some other tricks...

---

## Comment 3

> Username: olk  
> Created at: 2017-09-25 18:18:12 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/14#issuecomment-331968068  

What tells the compiler if the you add BOOST_CONTEXT_DECL at the front (line 66)?

---

## Comment 4

> Username: olk  
> Created at: 2017-09-26 13:12:52 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/14#issuecomment-332193653  

From the error C2492 reference: "The address of the thread variable is not known until run time, so it cannot be linked to a DLL import or export."

---

## Comment 5

> Username: olk  
> Created at: 2017-10-02 12:22:56 UTC  
> Updated at: 2017-10-02 13:05:07 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/14#issuecomment-333518421  

I've updated boost.context (branch develop). Could you check if the error regarding to the DLL interface has vanished?

---

## Comment 6

> Username: olk  
> Created at: 2017-10-26 06:35:02 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/14#issuecomment-339565882  

no, response - so closed

---

## Comment 7

> Username: jeking3  
> Created at: 2018-06-17 12:20:47 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/14#issuecomment-397875144  

It looks like we need to add Appveyor CI to validate things like this.
