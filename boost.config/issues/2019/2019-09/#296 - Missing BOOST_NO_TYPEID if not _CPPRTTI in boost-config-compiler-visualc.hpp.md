# #296 - Missing BOOST_NO_TYPEID if not _CPPRTTI in boost/config/compiler/visualc.hpp [Closed]

> Username: k15tfu  
> Created at: 2019-09-11 10:35:49 UTC  
> Updated at: 2019-09-11 11:07:17 UTC  
> Closed at: 2019-09-11 11:07:17 UTC  
> Url: https://github.com/boostorg/config/issues/296  

Looks like we forgot to define BOOST_NO_TYPEID if RTTI is disabled for MSVC (/GR- option).  
  
I can prepare a PR.

---

## Comment 1

> Username: MarcelRaad  
> Created at: 2019-09-11 10:40:43 UTC  
> Url: https://github.com/boostorg/config/issues/296#issuecomment-530324999  

I don't think that's a bug. Without RTTI, you can use `typeid`. You only cannot use *dynamic* typeid.

---

## Comment 2

> Username: k15tfu  
> Created at: 2019-09-11 10:48:55 UTC  
> Updated at: 2019-09-11 10:59:58 UTC  
> Url: https://github.com/boostorg/config/issues/296#issuecomment-530327521  

@MarcelRaad Thanks for a quick reply!  
  
But MSDN says you shouldn't (sounds like that) use typeid with /GR disabled:  
  
> You usually need the /GR option when your code uses dynamic_cast Operator or typeid  
(https://docs.microsoft.com/en-us/cpp/build/reference/gr-enable-run-time-type-information)  
  
~And now such code results to: `warning C4541: 'typeid' used on polymorphic type 'Base' with /GR-; unpredictable behavior may result`. https://godbolt.org/z/mp3YEG~

---

## Comment 3

> Username: k15tfu  
> Created at: 2019-09-11 11:07:17 UTC  
> Url: https://github.com/boostorg/config/issues/296#issuecomment-530332989  

Okay, I got you: BOOST_NO_RTTI is about dynamic_cast and dynamic typeid (https://www.boost.org/doc/libs/1_71_0/libs/exception/doc/configuration_macros.html), but BOOST_NO_TYPEID is about typeid in general. Thank you!
