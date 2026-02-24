# #177 - boost_stacktrace_impl_return_nullptr as an exported symbol [Closed]

> Username: lufriem  
> Created at: 2024-08-27 07:50:47 UTC  
> Updated at: 2024-09-13 20:28:14 UTC  
> Closed at: 2024-09-13 17:42:32 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/177  

#159 added the 'stacktrace from current exception for MSVC' feature but one (I guess) sideeffect is that I now get an exported symbol 'boost_stacktrace_impl_return_nullptr', which makes MSVS always generate an input library (.lib) for my (non-library, ie EXE) project (its linker just triggers off the __declspec(dllexport) tokens, with no way of suppressing this, see https://developercommunity.visualstudio.com/t/why-is-vs2017-building-an-import-library-for-my-ex/233569#T-N237446-N243028). Due to some (admittedly dodgy) project setup I ran into troubles with this file present (when it did not exist before), which I fixed on my side, but I'm wondering about this export..  
  
I guess there's a reason for having that symbol be exported? I see that using the BOOST_SYMBOL_EXPORT macro I guess I could suppress its creation, but I wouldn't really want to deviate from your default project/configuration defaults..  
  
Sorry for the rambling, I guess my question is, is this expected and something that users just have to deal with?  
  
Thank you

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-09-12 07:43:11 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/177#issuecomment-2345503306  

Make a PR with a fix and let the CI run. We'll see, if removing that `BOOST_SYMBOL_EXPORT` breaks something

---

## Comment 2

> Username: huangqinjin  
> Created at: 2024-09-12 14:26:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/177#issuecomment-2346456858  

You may use `/NOIMPLIB` linker option to suppress the generation of `.lib` file. Without `BOOST_SYMBOL_EXPORT`, no symbol `boost_stacktrace_impl_return_nullptr` exist and `/ALTERNATENAME` would not work.

---

## Comment 3

> Username: lufriem  
> Created at: 2024-09-12 14:36:38 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/177#issuecomment-2346483769  

Thank you both for your replies! I also searched around a bit but I'm afraid I'm out of my depth in regards to the boost build system.  
  
One thing I saw that I neglected to mention (sorry!): I'm using a static build of the boost libraries. I found [an old thread discussing the 'issue' of boost exporting symbols (also in the context of exception reporting, funnily enough)](https://github.com/boostorg/config/issues/243), I suppose under these circumstances (static lib) it would also be possible to globally suppress their creation (as they wouldn't be needed/wanted anyway) using the BOOST_DISABLE_EXPLICIT_SYMBOL_VISIBILITY switch. Under any other circumstances (ie shared library) I don't think that would be a good idea.

---

## Comment 4

> Username: huangqinjin  
> Created at: 2024-09-12 14:42:44 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/177#issuecomment-2346499609  

I mean to apply `/NOIMPLIB` to your EXE linker option, not the boost library itself.

---

## Comment 5

> Username: lufriem  
> Created at: 2024-09-12 14:46:00 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/177#issuecomment-2346508019  

Yes thank you I understood that, I was just wondering whether there was still something that could/should be done to the library itself

---

## Comment 6

> Username: huangqinjin  
> Created at: 2024-09-12 14:56:43 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/177#issuecomment-2346536208  

I have no idea if there is a better method to mimic the `__attribute__((weak))` behavior. Maybe just provide a macro to `#ifdef`-out the symbol if `boost_stacktrace_from_exception` is linked.

---

## Comment 7

> Username: apolukhin  
> Created at: 2024-09-12 17:49:27 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/177#issuecomment-2346897818  

> Maybe just provide a macro to #ifdef-out the symbol if boost_stacktrace_from_exception is linked.  
  
Hm, the  `BOOST_STACKTRACE_DYN_LINK ` macro is defined if dynamic library is being built. I'll try the ifdefing tomorrow

---

## Comment 8

> Username: lufriem  
> Created at: 2024-09-13 20:28:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/177#issuecomment-2350105154  

Thank you both for your efforts!
