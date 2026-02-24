# #2758 - Deprecating bind_handler and bind_front_handler in favor of Asio's append and prepend adapters [Closed]

> Username: ashtum  
> Created at: 2023-10-26 04:33:38 UTC  
> Updated at: 2023-12-30 05:17:34 UTC  
> Closed at: 2023-12-30 05:17:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2758  

In regard to the existing [append](https://www.boost.org/doc/libs/1_83_0/doc/html/boost_asio/overview/composition/token_adapters.html#boost_asio.overview.composition.token_adapters.append) and [prepend](https://www.boost.org/doc/libs/1_83_0/doc/html/boost_asio/overview/composition/token_adapters.html#boost_asio.overview.composition.token_adapters.prepend) completion token adapters, we can deprecate the [bind_handler](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__bind_handler.html) and [bind_front_handler](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__bind_front_handler.html) functions. We will also update the documentation to direct users towards using Asio's adapters instead.

---

## Comment 1

> Username: owacoder  
> Created at: 2023-11-04 19:15:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2758#issuecomment-1793531669  

Please note that the ASIO master has been updated to remove the bind_handler hooks so Beast is currently broken and not able to build out-of-the-box:  
  
    beast\core\detail\bind_handler.hpp(18,10): fatal  error C1083: Cannot open include file: 'boost/asio/handler_alloc_hook.hpp': No such file or directory  
  
Removed in [this ASIO commit](https://github.com/boostorg/asio/commit/57a8c85f6d91cdc5b701c7702f0e4215d07129bc) which got merged to master [here](https://github.com/boostorg/asio/commit/19320f53d14ce67864fc9ac987b1a703afe506f8).  
  
Beast is broken in the Boost master branch because of this.

---

## Comment 2

> Username: ashtum  
> Created at: 2023-11-04 19:40:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2758#issuecomment-1793537812  

@owacoder I believe we have already addressed this by merging this pr into the master: https://github.com/boostorg/beast/pull/2757  
Are you experiencing any issues when using the code in the master branch?

---

## Comment 3

> Username: owacoder  
> Created at: 2023-11-04 20:50:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2758#issuecomment-1793551029  

@ashtum Yes, I have git submodules set up to track and update to the parent Boost master. The submodule is never modified other than pulling updates:  
  
    git submodule update --init -- recursive  
    git submodule update --remote  
  
And then build with Boost.Build.  
  
This past week when updating I received the error above with Beast.

---

## Comment 4

> Username: ashtum  
> Created at: 2023-11-05 05:47:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2758#issuecomment-1793645297  

@owacoder, I've re-run the CI, and it built successfully. Please ensure that the latest commit in Boost.Beast master branch is 49ec654.

---

## Comment 5

> Username: owacoder  
> Created at: 2023-11-06 14:02:56 UTC  
> Updated at: 2023-11-06 14:09:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2758#issuecomment-1794893009  

@ashtum I've rebuilt from a fresh repository (our code and Boost) and the behavior is still present. I've also double-checked that the master branch commit is indeed 49ec654.  
  
It does appear that [bind_handler.hpp](https://github.com/boostorg/beast/blob/master/include/boost/beast/core/detail/bind_handler.hpp) is correct in Beast master but doesn't seem to be updated in Boost master? Not sure what's happening, it could be an issue in our repository... I'll keep checking.

---

## Comment 6

> Username: owacoder  
> Created at: 2023-11-06 15:30:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2758#issuecomment-1795098199  

@ashtum - Looks like it was a detached submodule issue, false alarm. Sorry for the confusion! Causing all submodules to recursively track as well as the parents fixed the issue. If someone else has the same issue I suppose the comments here may help.

---

## Comment 7

> Username: ashtum  
> Created at: 2023-11-06 15:50:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2758#issuecomment-1795192553  

> @ashtum - Looks like it was a detached submodule issue, false alarm. Sorry for the confusion!  
  
No problem, thank you for reporting this in the first place.
