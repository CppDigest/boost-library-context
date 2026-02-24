# #59 fix logic for non-uwp build api selection [Closed]

> Username: jeking3  
> Created at: 2017-10-07 14:26:58 UTC  
> Updated at: 2017-10-07 17:15:05 UTC  
> Closed at: 2017-10-07 17:15:05 UTC  
> Url: https://github.com/boostorg/winapi/pull/59  

I did not rebuild the other boost libraries after refactoring for the Boost.Predef changes we made.  I needed to modify the partition selectors to include APIs if the platform doesn't support UWP development.  This didn't make it into the larger pull request, unfortunately.  This allows me to build other boost libraries like Boost.System., Boost.Chrono, etc.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-10-07 14:36:53 UTC  
> Url: https://github.com/boostorg/winapi/pull/59#issuecomment-334939383  

I already have local changes to config.hpp, also to the effect of this PR, so I will not merge it.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-07 14:38:17 UTC  
> Url: https://github.com/boostorg/winapi/pull/59#issuecomment-334939469  

Okay that's fine - I just wanted to jump on this as quickly as possible to avoid folks running into it.  Can you leave this open until you merge something into develop?  I'm blocked submitting my work to make Boost.Random a header-only library (and add UWP support to the random_device()) so once you close this I can submit that.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-10-07 17:15:04 UTC  
> Url: https://github.com/boostorg/winapi/pull/59#issuecomment-334951321  

See https://github.com/boostorg/winapi/commit/8091170850cc0836d24d15e38de004aee35e3ee0.

---
