# #1015 test fix for multiprecision 562 [Merged]

> Username: ryanelandt  
> Created at: 2023-08-20 15:35:19 UTC  
> Updated at: 2023-08-23 13:22:48 UTC  
> Merged at: 2023-08-23 13:17:45 UTC  
> Closed at: 2023-08-23 13:17:45 UTC  
> Url: https://github.com/boostorg/math/pull/1015  

[According to this](https://en.cppreference.com/w/cpp/types/numeric_limits/denorm_min), if a floating point type `T` supports subnormal numbers, then `std::numeric_limits<T>::denorm_min()` is a subnormal number. If support is absent, then `std::numeric_limits<T>::denorm_min()` is `std::numeric_limits<T>::min()`.  
  
There is a test in `test_autodiff_7.cpp` that checks that `std::numeric_limits<T>::denorm_min()` is not a normal number. This test passes even though the statement is sometimes false. This result is due to an oversight in Boost Multiplication to be corrected by [this PR](https://github.com/boostorg/multiprecision/pull/562).  
  
This PR updates this incorrect test so that Boost Math doesn't breaking when the above mentioned Boost Multiprecision PR gets merged.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2023-08-20 16:31:13 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1685325090  

I'm not the expert here, but somehow my feeling might be to handle/agree to this one, when/if possible. After that, then https://github.com/boostorg/multiprecision/pull/562  
  
Thoughts?

---

## Comment 2

> Username: ryanelandt  
> Created_at: 2023-08-20 17:34:37 UTC  
> Updated_at: 2023-08-20 17:35:31 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1685349038  

As to the order of the two PRs, there are two schools of thoughts:  
1. The purpose of unit tests is _not_  that they pass, but that they check for correctness. If Boost Math tests require incorrect values values of `std::numeric_limits<T>::denorm_min()` to pass, then having these tests fail is the desired result. Enforcing correctness in Boost Multiprecision is the responsibility of that library's CI. Holding up a PR because it may/will break tests in external libraries is not sensible.  If it broke test in private libraries, how would we even know?  
2. Libraries should avoid making changes that break the code of downstream users. Mediating steps can be taking that include deprecation warnings. In extreme cases, this means depreciating a library, and creating a new one. I think this is what happened when https://github.com/boostorg/signals was depreciated in favor of https://github.com/boostorg/signals2.  
  
I think that approach 2 is sensible when it comes to API changes, but not so much in this situation. I don't know which approach is the Boost way. Hopefully someone in the know can weight in. In any case, this may be a moot point. I think this is a good candidate for a speedy merge.

---

## Comment 3

> Username: pulver  
> Created_at: 2023-08-20 17:57:15 UTC  
> Updated_at: 2023-08-20 18:00:25 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1685353758  

Can the type `T` be checked with [has_denorm](https://en.cppreference.com/w/cpp/types/numeric_limits/has_denorm) in order to verify the applicability of tests such as  
```  
    BOOST_CHECK(  
        !isnormal(make_fvar<T, m>(std::numeric_limits<T>::denorm_min())));  
```  
?  
  
I see `has_denorm` is marked `deprecated in C++23` but perhaps an equivalent test can be used for later compilers?  
  
If not, it might be useful to instead comment out the test with a reference back to this discussion in case someone wonders about this issue.

---

## Comment 4

> Username: ryanelandt  
> Created_at: 2023-08-20 18:22:29 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1685359118  

> Can the type T be checked with [has_denorm](https://en.cppreference.com/w/cpp/types/numeric_limits/has_denorm) in order to verify the applicability of tests such as  
  
I think that's what's going on in this PR. The `if` statement on line 52 checks `has_denorm`:  
```  
if (std::numeric_limits<T>::has_denorm != std::denorm_absent) {  
```  
I added an `else` statement to this and moved the relevant test inside the `else` block. Addressing future deprecation issues may be out of the scope of this PR.  
  
In a future without `has_denorm`, I think the relevant test is `0 < denorm_min() <= min()`.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2023-08-21 04:32:05 UTC  
> Updated_at: 2023-08-21 04:33:59 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1685624904  

Hi John (@jzmaddock) or Matt (@mborland), could you take a quick look?  
  
Looks trivial and as soon as a tiny review is finished, we can handle both this PR as well as the [one in Multiprecision](https://github.com/boostorg/multiprecision/pull/562).

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2023-08-21 08:42:43 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1685905650  

I think we're over-thinking this a bit, we know the two libraries are a little entangled, lets just get the multiprecision version merged/fixed, then fix this one properly rather than messing about with #if's.

---

## Comment 7

> Username: pulver  
> Created_at: 2023-08-21 13:03:48 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1686290075  

> > Can the type T be checked with [has_denorm](https://en.cppreference.com/w/cpp/types/numeric_limits/has_denorm) in order to verify the applicability of tests such as  
>   
> I think that's what's going on in this PR. The `if` statement on line 52 checks `has_denorm`:  
>   
> ```  
> if (std::numeric_limits<T>::has_denorm != std::denorm_absent) {  
> ```  
>   
> I added an `else` statement to this and moved the relevant test inside the `else` block.  
  
In that case, it seems to make sense to move the existing test  
```  
    BOOST_CHECK(  
        !isnormal(make_fvar<T, m>(std::numeric_limits<T>::denorm_min())));  
```  
into the existing `if` block above that checks `has_denorm`, rather than deleting it. I believe this applies irrespective of the multiprecision changes.

---

## Comment 8

> Username: ryanelandt  
> Created_at: 2023-08-21 13:23:30 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1686323823  

@jzmaddock   
> I think we're over-thinking this a bit, we know the two libraries are a little entangled, lets just get the multiprecision version merged/fixed, then fix this one properly rather than messing about with #if's.  
  
I agree, sounds great.

---

## Comment 9

> Username: ryanelandt  
> Created_at: 2023-08-21 13:40:36 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1686353230  

@pulver  
  
> In that case, it seems to make sense to move the existing test  
>   
> ```  
>     BOOST_CHECK(  
>         !isnormal(make_fvar<T, m>(std::numeric_limits<T>::denorm_min())));  
> ```  
>   
> into the existing `if` block above that checks `has_denorm`, rather than deleting it. I believe this applies irrespective of the multiprecision changes.  
  
I looked at this file more carefully to understand what it is trying to do. It seems that it is testing to see if `fpclassify.hpp` operates correctly for an autodiff type. I think that `fpclassify.hpp` is currently tested for various types in the file `test_classify.cpp`. Would testing the autodiff type in `test_classify.cpp` cover the same things as the tests in this file?

---

## Comment 10

> Username: pulver  
> Created_at: 2023-08-21 14:00:52 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1686392570  

> I looked at this file more carefully to understand what it is trying to do. It seems that it is testing to see if `fpclassify.hpp` operates correctly for an autodiff type. I think that `fpclassify.hpp` is currently tested for various types in the file `test_classify.cpp`. Would testing the autodiff type in `test_classify.cpp` cover the same things as the tests in this file?  
  
That sounds like a good idea for both autodiff as well as multiprecision types. Though I can't speak to how best to setup the dependencies (i.e. Add `test_classify` tests to `autodiff`/`multiprecision` tests, or insert `autodiff`/`multiprecision` tests into `test_classify.cpp`?)

---

## Comment 11

> Username: ryanelandt  
> Created_at: 2023-08-21 14:53:43 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1686485968  

I added the autodiff types to the classify test, which seems cleaner to me. Does this seem like the correct thing to you?

---

## Comment 12

> Username: pulver  
> Created_at: 2023-08-21 15:21:28 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1686536557  

> I added the autodiff types to the classify test, which seems cleaner to me. Does this seem like the correct thing to you?  
  
Yes, seems that's how it should have originally been done, assuming it's ok w/ @jzmaddock et al.  
  
Thank you!

---

## Comment 13

> Username: ryanelandt  
> Created_at: 2023-08-21 20:02:23 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1686962865  

@jzmaddock The failures seem unrelated.

---

## Comment 14

> Username: mborland  
> Created_at: 2023-08-23 13:17:32 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1689949828  

This looks good to me. Math and Multiprecision are pretty tightly coupled so this is not the first time that we have had to make changes in one that impacts the test results in another.

---

## Comment 15

> Username: ryanelandt  
> Created_at: 2023-08-23 13:22:43 UTC  
> Url: https://github.com/boostorg/math/pull/1015#issuecomment-1689957950  

Thanks @mborland

---
