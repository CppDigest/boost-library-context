# #21 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74 [Merged]

> Username: eldiener  
> Created at: 2020-04-10 15:59:55 UTC  
> Updated at: 2020-05-02 15:15:00 UTC  
> Merged at: 2020-05-02 15:15:00 UTC  
> Closed at: 2020-05-02 15:15:00 UTC  
> Url: https://github.com/boostorg/integer/pull/21  



---

## Review 1 [Changes requested]

> Username: Lastique  
> Created_at: 2020-05-01 22:55:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/integer/pull/21#pullrequestreview-404437413  

📁 include/boost/integer/integer_log2.hpp

```diff
  18 | #include <assert.h>
  19 |- #ifdef __BORLANDC__
  19 |+ #if defined(__BORLANDC__) && !defined(__clang__)
```

> Username: Lastique  
> Created_at: 2020-05-01 22:50:49 UTC  
> Updated_at: 2020-05-01 22:55:08 UTC  
> Url: https://github.com/boostorg/integer/pull/21#discussion_r418769630  

Shouldn't this one become `defined(BOOST_BORLANDC)`?

> Username: eldiener  
> Created_at: 2020-05-01 23:02:53 UTC  
> Url: https://github.com/boostorg/integer/pull/21#discussion_r418772483  

No. Boost config.hpp is not being included at that point so that I must use the long form of checking for BOOST_BORLANDC.

> Username: Lastique  
> Created_at: 2020-05-01 23:10:51 UTC  
> Updated_at: 2020-05-01 23:11:37 UTC  
> Url: https://github.com/boostorg/integer/pull/21#discussion_r418774331  

`boost/config.hpp` is included just below, so this check and the guarded include could be moved below that one.

> Username: eldiener  
> Created_at: 2020-05-02 00:08:01 UTC  
> Url: https://github.com/boostorg/integer/pull/21#discussion_r418785410  

Yes, but why bother. There is nothing wrong with the code as it now is.

> Username: Lastique  
> Created_at: 2020-05-02 07:49:20 UTC  
> Updated_at: 2020-05-02 07:49:21 UTC  
> Url: https://github.com/boostorg/integer/pull/21#discussion_r418927242  

I thought the whole point of these macros was to remove code duplication.

> Username: eldiener  
> Created_at: 2020-05-02 13:28:52 UTC  
> Url: https://github.com/boostorg/integer/pull/21#discussion_r418959156  

The point of the macros was to use them as shorthand when config.hpp had already been included, else use the longhand check.  The check is so that the Embarcadero C++ clang-based compilers were not included in workarounds for the old bcc32 compiler. But I have no intention of changing the order of code just so I can use the shorthand macros instead of the longhand method.

> Username: glenfe  
> Created_at: 2020-05-02 13:42:43 UTC  
> Url: https://github.com/boostorg/integer/pull/21#discussion_r418960671  

@eldiener Now that there are two BCC compilers that ship with Embarcadero C++ Builder: BCC32X (the clang based one) and BCC32C (the one compatible with the older Borland C++ BCC32 compiler), does   `defined(__BORLANDC__) && !defined(__clang__)` apply to BCC32C too, or just ancient BCC32?  (Either way, if you're touching code in Integer which @Lastique maintains, and he would prefer consistency among all Borland compiler checks, it seems like a reasonable request).

> Username: eldiener  
> Created_at: 2020-05-02 14:04:27 UTC  
> Updated_at: 2020-05-02 14:08:05 UTC  
> Url: https://github.com/boostorg/integer/pull/21#discussion_r418962888  

Embarcadero C++ has four Windows compilers. The bcc32 is the old Windows compiler and does not define \_\_clang\_\_, and is what all the old workaround code is all about. The bcc32c and bcc32x are 32-bit clang-based compilers, both defining \_\_clang\_\_ and the only difference between the two is that bcc32c takes the same command line options that the old bcc32 takes while bcc32x takes clang-based command line options. Finally their is bcc64, which is a 64-bit compiler, defining \_\_clang\_\_, which takes clang-based command line options. None of the clang-based compilers should need to be using the old bcc32 workarounds Boost has in its code. I have no interest in bcc32 and I really doubt that even with the workarounds in Boost code that it can currently compile more than about 5-10 percent of current Boost successfully. My only interest is to have the clang-based compilers work with current Boost on an ongoing basis and it is those compilers I test when making PRs and issuing changes.   
  
As far as consistency goes I have already explained that if config.hpp is included then the macros BOOST_BORLANDC, BOOST_CODEGEARC, and BOOST_EMBTC can be used, but if config.hpp has not been included than the checks for \_\_BORLANDC\_\_ and !\_\_clang\_\_, \_\_CODEGEARC\_\_ and !\_\_clang\_\_, and \_\_CODEGEARC\_\_ and \_\_clang\_\_ are just as good. I am actually trying to change code as little as possible by not moving around header files. But if he insists that I move around his code so that config.hpp is earlier in the file so that the shorthand can be used I will do it. Or of course he can do it after he merges the PR.

> Username: Lastique  
> Created_at: 2020-05-02 15:14:30 UTC  
> Url: https://github.com/boostorg/integer/pull/21#discussion_r418969994  

But `config.hpp` is included, literally a few lines lower. Not using the macros from it, when the whole point of the PR is to port to those macros, makes no sense. Of course, I can fix it myself, and apparently I'll have to.


---
