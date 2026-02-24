# #688 - Building project results in compilation error with Xcode 14 beta IDE [Open]

> Username: JoshChang-TomTom  
> Created at: 2022-09-20 03:12:12 UTC  
> Updated at: 2023-01-26 00:24:39 UTC  
> Url: https://github.com/boostorg/boost/issues/688  

Apple announced the new Xcode 14 beta IDE.  
It causes 3 build errors in dependent Boost lib which uses deprecated sprintf:  
  
> Boost/headers/lexical_cast/detail/converter_lexical_streams.hpp  
> 'sprintf' is deprecated: This function is provided for compatibility reasons only. Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead.  
  
Extra information:  
Boost version used: 1_75_0 (The latest version 1_80_0 has the same issue)  
  
Use Xcode 14 Compiler:  
> Apple clang version 14.0.0 (clang-1400.0.28.1)  
  
On previous Xcode 13, it worked well.  
Xcode 13 Compiler:  
> Apple clang version 13.1.6 (clang-1316.0.21.2.5)

---

## Comment 1

> Username: mclow  
> Created at: 2022-11-15 22:21:12 UTC  
> Url: https://github.com/boostorg/boost/issues/688#issuecomment-1315940318  

Warning, not error.  But still...

---

## Comment 2

> Username: cpsauer  
> Created at: 2022-11-16 00:48:55 UTC  
> Url: https://github.com/boostorg/boost/issues/688#issuecomment-1316101331  

Indeed, though not obvious to users that there isn't a real security issue here?  
I know that sprintf calls are also present in other places, like regex

---

## Comment 3

> Username: Hadatko  
> Created at: 2023-01-25 23:33:50 UTC  
> Url: https://github.com/boostorg/boost/issues/688#issuecomment-1404360656  

Hello, isn't better to replace sprintf with snprintf? Using '-Wno-deptecated-declarations' you are forcing other projects to use that too. For example we are threating to all warnings as errors.  
  
`/opt/homebrew/Cellar/boost/1.81.0_1/include/boost/lexical_cast/detail/converter_lexical_streams.hpp:310:21: error: 'sprintf' is deprecated: This function is provided for compatibility reasons only.  Due to security concerns inherent in the design of sprintf(3), it is highly recommended that you use snprintf(3) instead. [-Werror,-Wdeprecated-declarations]  
`  
https://github.com/EmbeddedRPC/erpc/issues/342

---

## Comment 4

> Username: mclow  
> Created at: 2023-01-26 00:06:26 UTC  
> Url: https://github.com/boostorg/boost/issues/688#issuecomment-1404381087  

In lexical_cast, this has been resolved (see https://github.com/boostorg/lexical_cast/pull/56). It will be in the next boost release.

---

## Comment 5

> Username: Hadatko  
> Created at: 2023-01-26 00:24:38 UTC  
> Url: https://github.com/boostorg/boost/issues/688#issuecomment-1404396966  

Oh great, 👍👍👍
