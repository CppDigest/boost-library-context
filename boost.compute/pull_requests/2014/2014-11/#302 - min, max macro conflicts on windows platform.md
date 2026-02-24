# #302 min, max macro conflicts on windows platform [Merged]

> Username: 9prady9  
> Created at: 2014-11-07 19:40:50 UTC  
> Updated at: 2014-11-08 06:43:06 UTC  
> Merged at: 2014-11-08 06:35:34 UTC  
> Closed at: 2014-11-08 06:35:34 UTC  
> Url: https://github.com/boostorg/compute/pull/302  

min, max functions declarations in functional/integer header were causing compilation issues on windows. This could be related to min, max macros being used in other projects that use compute. The temporary fix i did in our project was to undef min, max before every boost/compute header inclusion which was causing the issue. Windows Development Environment i used is Visual Studio 2013.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-11-07 19:56:28 UTC  
> Url: https://github.com/boostorg/compute/pull/302#issuecomment-62202672  

[![Coverage Status](https://coveralls.io/builds/1443735/badge)](https://coveralls.io/builds/1443735)  
  
Coverage remained the same when pulling **4730efd619f5a4dd9278e7428926a82188ffa08f on 9prady9:windows_macro_errors** into **c5cd43bf0b62fea3382ffc3c724019ca5f4ca17c on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-11-07 20:06:17 UTC  
> Url: https://github.com/boostorg/compute/pull/302#issuecomment-62203970  

Ahh, annoying Windows and their min/max macros...  
  
For this however I don't think it's safe to just `#undef` them in a public header. I think a better fix would be to add parentheses around `name` in "functional/detail/macros.hpp" to prevent macro-expansion:  
  
```  
class name : public function<signature> \  
{ \  
public: \  
    (name)() : function<signature>(BOOST_PP_STRINGIZE(name)) { } \  
};  
```  
  
Can you try this out?

---

## Comment 3

> Username: 9prady9  
> Created_at: 2014-11-07 20:07:49 UTC  
> Url: https://github.com/boostorg/compute/pull/302#issuecomment-62204172  

Sure, will try it out and let you know.

---

## Comment 4

> Username: 9prady9  
> Created_at: 2014-11-07 20:20:56 UTC  
> Url: https://github.com/boostorg/compute/pull/302#issuecomment-62205929  

parentheses around `name` worked fine. I will revert my changes and push this change.

---

## Comment 5

> Username: ddemidov  
> Created_at: 2014-11-07 20:50:35 UTC  
> Url: https://github.com/boostorg/compute/pull/302#issuecomment-62209695  

Another option would be defining NOMINMAX macro globally (for example, see [here](https://github.com/ddemidov/vexcl/blob/2537f21539dde7fe952eb13aa1ec838f5b1b9285/CMakeLists.txt#L82))

---

## Comment 6

> Username: coveralls  
> Created_at: 2014-11-07 20:55:49 UTC  
> Url: https://github.com/boostorg/compute/pull/302#issuecomment-62210336  

[![Coverage Status](https://coveralls.io/builds/1443966/badge)](https://coveralls.io/builds/1443966)  
  
Coverage remained the same when pulling **505d1ac0ec4a9959f68aca41cfeaacb9009dafa8 on 9prady9:windows_macro_errors** into **52eab193e784c792669a9af59ec277f6850c1f44 on kylelutz:develop**.

---

## Comment 7

> Username: 9prady9  
> Created_at: 2014-11-07 20:55:50 UTC  
> Url: https://github.com/boostorg/compute/pull/302#issuecomment-62210342  

@ddemidov  i think i tried NOMINMAX definition and it didn't work, may be i did something else wrong.

---

## Comment 8

> Username: 9prady9  
> Created_at: 2014-11-08 06:32:29 UTC  
> Url: https://github.com/boostorg/compute/pull/302#issuecomment-62247936  

@ddemidov adding that definition globally like you suggested worked. I was doing a NOMINMAX in one of the main headers but apparently another header was using windows.h too. thanks :+1:   
  
@kylelutz please go ahead and close the issue if it's not needed.

---

## Comment 9

> Username: kylelutz  
> Created_at: 2014-11-08 06:35:21 UTC  
> Updated_at: 2014-11-08 06:42:34 UTC  
> Url: https://github.com/boostorg/compute/pull/302#issuecomment-62247980  

I think both are good, I'll merge this change. The Boost.Compute headers should defend themselves against macros like these and also client code can/should define the `NOMINMAX` macro to prevent these sorts of issues. Thanks for reporting this and for the fix!

---
