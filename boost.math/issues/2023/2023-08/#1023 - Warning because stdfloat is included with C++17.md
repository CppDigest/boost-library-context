# #1023 - Warning because stdfloat is included with C++17 [Closed]

> Username: ChristophStrehle  
> Created at: 2023-08-28 07:56:18 UTC  
> Updated at: 2023-08-28 17:10:59 UTC  
> Closed at: 2023-08-28 17:10:59 UTC  
> Url: https://github.com/boostorg/math/issues/1023  

When I include boost/math/concepts/real_concept.hpp or boost/math/tools/promotion.hpp from boost 1.83 I get the following compiler warning with Visual Studio 17.7 and the v143 version 14.37 toolset compiling for C++17:  
  
```warning STL4038: The contents of <stdfloat> are available only with C++23 or later.```  
  
Looks like stdfload is there but should not be included with C++17. The two includes are here:  
https://github.com/boostorg/math/blob/develop/include/boost/math/tools/promotion.hpp#L31  
https://github.com/boostorg/math/blob/develop/include/boost/math/concepts/real_concept.hpp#L49  
  
Btw I don't get this warning when I include boost/math/tools/config.hpp:  
https://github.com/boostorg/math/blob/develop/include/boost/math/tools/config.hpp#L157

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-08-28 08:51:38 UTC  
> Url: https://github.com/boostorg/math/issues/1023#issuecomment-1695298431  

That's a bug, tools/config.hpp is doing it correctly (checking the std version first).  
  
I should add some Boost.Config macros for the new C++23 headers.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2023-08-28 09:18:53 UTC  
> Url: https://github.com/boostorg/math/issues/1023#issuecomment-1695340468  

We were just tuning the exact header doing the recognition of this header. We used the macro `__has_include` exclusively.  
  
John, do you think we should combine thas PP querry with a check on `__cplusplus` to ensure that it is also _high_ enough?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2023-08-28 14:41:05 UTC  
> Url: https://github.com/boostorg/math/issues/1023#issuecomment-1695823681  

>We were just tuning the exact header doing the recognition of this header. We used the macro __has_include exclusively.  
>  
>John, do you think we should combine thas PP querry with a check on __cplusplus to ensure that it is also high enough?  
  
You always have to do that: __has_Include only tells you whether the file exists on disk, not whether you can actually include it in the current -std= mode.  That's why we have Boost.Config macros to handle this and make it less error prone, I just haven't added those for C++23 yet!

---

## Comment 4

> Username: mborland  
> Created at: 2023-08-28 14:47:00 UTC  
> Url: https://github.com/boostorg/math/issues/1023#issuecomment-1695833485  

That's my mistake. I'll just keep going with greater than `202002L` because there's no macro definition for C++23 yet, and `<stdfloat>` does not have a feature test macro either: https://en.cppreference.com/w/cpp/feature_test

---

## Comment 5

> Username: ckormanyos  
> Created at: 2023-08-28 15:56:19 UTC  
> Url: https://github.com/boostorg/math/issues/1023#issuecomment-1695942486  

>  keep going with greater than `202002L` because there's no macro definition for C++23 yet, and `<stdfloat>` does not have a feature test macro either  
  
Awesome plan. Before I wrack my brain with the PP logic, could you please do this Matt, as you're more practiced at it than I?

---

## Comment 6

> Username: ckormanyos  
> Created at: 2023-08-28 15:59:14 UTC  
> Url: https://github.com/boostorg/math/issues/1023#issuecomment-1695946677  

Ahhh you already beat me to it with #1023 sorry about the noise. Thanks guys and thanks Christophe (@ChristophStrehle)
