# #328 Further changes for Embarcadero C++ clang-based compilers [Merged]

> Username: eldiener  
> Created at: 2020-04-05 21:44:15 UTC  
> Updated at: 2020-04-07 20:56:05 UTC  
> Merged at: 2020-04-07 20:56:04 UTC  
> Closed at: 2020-04-07 20:56:04 UTC  
> Url: https://github.com/boostorg/config/pull/328  

I discovered through testing some issues and made further changes to the Embarcadero C++ clang-based compilers support in config. I also updated the test for support of the cwchars header file involving a basic test for multi-byte character support in the header file. The Embarcadero C++ clang-based compilers were passing the test but were actually missing all the multi-byte support in the header file, including all wide character to multi-byte and all the multi-byte to wide character functionality in the header file. So I felt updating the test slightly just to make sure that multi-byte support existed in its most basic form was necessary. I do not think this will affect any other compilers, and in my testing all the compilers I tested were not affected in any way by failing the test where previously they may have passed it.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-04-07 19:44:44 UTC  
> Url: https://github.com/boostorg/config/pull/328#issuecomment-610584103  

Any objections to merging this PR ?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-04-07 20:55:59 UTC  
> Url: https://github.com/boostorg/config/pull/328#issuecomment-610615830  

Nope, merging now....

---
