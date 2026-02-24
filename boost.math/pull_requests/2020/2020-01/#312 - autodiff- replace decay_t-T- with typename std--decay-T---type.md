# #312 autodiff: replace decay_t<T> with typename std::decay<T>::type. [Closed]

> Username: pulver  
> Created at: 2020-01-25 15:46:21 UTC  
> Updated at: 2020-01-28 13:00:31 UTC  
> Closed at: 2020-01-28 12:59:59 UTC  
> Url: https://github.com/boostorg/math/pull/312  

This is a fix for an issue brought up in #311. For compatibility with C++11, we will use the more verbose `typename std::decay<T>::type` rather than the more succinct C++14 version `std::decay_t<T>`.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-01-25 18:44:25 UTC  
> Url: https://github.com/boostorg/math/pull/312#issuecomment-578431624  

Haha, looks like we've both been at this, there's an alternative in https://github.com/boostorg/math/pull/313 which also removes the #include of special_functions.hpp in favour of just those we really need, and also adds a #include test case.

---

## Comment 2

> Username: pulver  
> Created_at: 2020-01-25 19:12:11 UTC  
> Url: https://github.com/boostorg/math/pull/312#issuecomment-578434439  

@jzmaddock Ok great once #313 passes then I'm happy to go with that over this PR. Cheers.

---

## Comment 3

> Username: pulver  
> Created_at: 2020-01-28 12:59:59 UTC  
> Url: https://github.com/boostorg/math/pull/312#issuecomment-579233920  

Use #313 instead.

---
