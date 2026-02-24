# #77 - automatic linking for VS projects [Open]

> Username: apotocki  
> Created at: 2019-05-10 07:35:02 UTC  
> Updated at: 2019-05-13 08:19:06 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/77  

Hi Anton! Why don't you use boost/config/auto_link.hpp to avoid manual linking for VS when one of the link macroses is defined?

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-05-10 07:43:50 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/77#issuecomment-491192428  

Hi there!  
  
There are different "backends" for the library https://www.boost.org/doc/libs/1_70_0/doc/html/stacktrace/configuration_and_build.html and it is impossible to predict which one are you going to use.  
  
For example in debug mode you may wish to link with **boost_stacktrace_windbg_cached** and in release mode sometimes you may wish to disable tracing via linking with **boost_stacktrace_noop**.

---

## Comment 2

> Username: apotocki  
> Created at: 2019-05-10 08:06:04 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/77#issuecomment-491199038  

I could specify my wishes by macroses you provided (e.g. BOOST_STACKTRACE_USE_WINDBG_CACHED or BOOST_STACKTRACE_USE_NOOP). But now I have to delve into naming, versioning and etc of the library.

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-05-13 07:08:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/77#issuecomment-491701911  

Hm... The manual linking in the library was intended for quick switching between **boost_stacktrace_windbg_cached** and  **boost_stacktrace_noop** without recompilation.  
  
Linking with appropriate library when the macro defined will kill that feature. But it will simplify library usage for users who do not need quick backend switching.  
  
Sounds like a good feature!

---

## Comment 4

> Username: apotocki  
> Created at: 2019-05-13 08:19:06 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/77#issuecomment-491723091  

For people who want the manual linking, you can introduce additional macros that would stop auto_link. BTW, are there other boost libraries that require manual linking on Windows by default?
