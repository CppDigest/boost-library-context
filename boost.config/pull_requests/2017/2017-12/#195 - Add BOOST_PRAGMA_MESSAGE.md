# #195 Add BOOST_PRAGMA_MESSAGE [Merged]

> Username: pdimov  
> Created at: 2017-12-04 00:47:31 UTC  
> Updated at: 2019-11-02 11:17:14 UTC  
> Merged at: 2017-12-12 18:15:52 UTC  
> Closed at: 2017-12-12 18:15:52 UTC  
> Url: https://github.com/boostorg/config/pull/195  

This PR adds `BOOST_PRAGMA_MESSAGE(M)`, a macro that expands to an equivalent on `#pragma message(M)`, when it's supported. In addition, on MSVC it adds `__FILE__` and `__LINE__` to the message, as the plain `#pragma message` has the problem that it's impossible to tell where the message originated.  
  
Since there are places where Boost.Config itself emits messages using the preprocessor, the best place for this utility macro is in Config so that it can be used inside.  
  
This PR includes #194. Also includes a few unrelated doc fixes and regenerates the HTML.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-01-13 18:53:57 UTC  
> Url: https://github.com/boostorg/config/pull/195#issuecomment-357457668  

John, would you please consider merging this to master?

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-01-13 18:55:46 UTC  
> Url: https://github.com/boostorg/config/pull/195#issuecomment-357457820  

In tests, I'm using  
  
```  
#if defined(BOOST_NO_CXX11_LAMBDAS)  
  
BOOST_PRAGMA_MESSAGE("Skipping test due to BOOST_NO_CXX11_LAMBDAS")  
int main() {}  
  
#else  
  
// ...  
```  
  
and I'm going to need to merge such tests to master at some point.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-01-13 19:11:23 UTC  
> Url: https://github.com/boostorg/config/pull/195#issuecomment-357458711  

Peter: my main machine is out of action at present, so I'm working off an old laptop that has no Boost or even a working compiler till I can figure out the part I need to fix the main one (dodgy lcd cable) :(  
  
Can you take care of this for now please?  Note that everything currently in develop could/should be merged to master really - especially all the msvc-15.5 stuff.

---

## Comment 4

> Username: pdimov  
> Created_at: 2018-01-13 19:39:24 UTC  
> Url: https://github.com/boostorg/config/pull/195#issuecomment-357460475  

OK, merged develop in https://github.com/boostorg/config/commit/19349c3941adc71a63f67a474a63b3c3f95bb502.

---
