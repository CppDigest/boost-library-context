# #193 - `binary-format` and `abi` detection does not support cross-compilation, nor multiarch [Closed]

> Username: Kojoley  
> Created at: 2021-12-16 20:41:15 UTC  
> Updated at: 2022-02-21 06:31:01 UTC  
> Closed at: 2022-02-21 06:24:28 UTC  
> Url: https://github.com/boostorg/context/issues/193  

Values for `binary-format` and `abi` are calculated using information about host, not the target:  
https://github.com/boostorg/context/blob/83f998828c377ebd27d32455154f1f9cebf09b93/build/Jamfile.v2#L57-L65  
https://github.com/boostorg/context/blob/83f998828c377ebd27d32455154f1f9cebf09b93/build/Jamfile.v2#L77-L86  
  
The actual user was hit by this https://github.com/bfgroup/b2/issues/105#issuecomment-996115240  
  
The logic also will yield incorrect results where a compiler is producing multiarch binaries, for example on apple clang it should at the same time deduce both `x86` and `arm` for `<architecture>`, and `sysv` and `aapcs` for `<abi>`.

---

## Comment 1

> Username: olk  
> Created at: 2021-12-17 07:32:13 UTC  
> Updated at: 2021-12-17 07:33:30 UTC  
> Url: https://github.com/boostorg/context/issues/193#issuecomment-996499210  

Support for multiarch/cross-comp. must be implemented inside boost.build.

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-12-17 23:07:46 UTC  
> Url: https://github.com/boostorg/context/issues/193#issuecomment-997082026  

Cross compilation is implemented for some toolsets in B2 already, the mentioned code above which is not compatible with cross compilation is in Context.

---

## Comment 3

> Username: olk  
> Created at: 2022-02-21 06:24:28 UTC  
> Updated at: 2022-02-21 06:31:01 UTC  
> Url: https://github.com/boostorg/context/issues/193#issuecomment-1046515161  

Boost.Build does not detect the ABI and BIANRY_FORMAT for which the code has to be compiled (I think there is no proper way to do it).  
The only way is that Boost.Context guesses the ABI and BINARY_FORMAT depending on the platform it is build on.  
For cross compilation you can override the properties at command line - for instance `abi=aapcs binary_format=elf`
