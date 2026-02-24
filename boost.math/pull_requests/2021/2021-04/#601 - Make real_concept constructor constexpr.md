# #601 Make real_concept constructor constexpr [Closed]

> Username: mborland  
> Created at: 2021-04-05 18:01:22 UTC  
> Updated at: 2021-04-11 12:45:50 UTC  
> Closed at: 2021-04-11 12:42:00 UTC  
> Url: https://github.com/boostorg/math/pull/601  

Make real_concept constructors and tools `constexpr` to avoid collision failures from issue #571.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-04-06 07:23:23 UTC  
> Url: https://github.com/boostorg/math/pull/601#issuecomment-813891163  

Morning Matt: I'm not sure this is the correct fix - it changes the interface to the concept, and in general, we _do_ want to allow non-constexpr constructors etc, as in the Multiprecision types for example.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-04-06 17:05:24 UTC  
> Url: https://github.com/boostorg/math/pull/601#issuecomment-814282983  

> we _do_ want to allow non-constexpr constructors etc, as in the Multiprecision types for example.  
  
Wouldn't that still be permissible with this change as long as they are used outside of a `constexpr` context? A broader question is how much effort should we put into fixes for ancient compilers like this issue? Is it worth putting in the notes that the oldest supported versions of GCC/Clang/MSVC are X.Y.Z? I feel like the current testing of GCC 5-10 (and I test on GCC11 locally) is pretty thorough.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-04-06 17:37:21 UTC  
> Url: https://github.com/boostorg/math/pull/601#issuecomment-814304736  

It's certainly pointless unless we're testing those compilers, otherwise we'll just see more regressions down the road.  I've added tentative testing to drone on https://github.com/boostorg/math/pull/602

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-04-07 17:40:39 UTC  
> Url: https://github.com/boostorg/math/pull/601#issuecomment-815099712  

> A broader question is how much effort should we put into fixes for ancient compilers like this issue? Is it worth putting in the notes that the oldest supported versions of GCC/Clang/MSVC are X.Y.Z? I feel like the current testing of GCC 5-10 (and I test on GCC11 locally) is pretty thorough.  
  
I'm beginning to think that gcc-4.8 and 4.9 are too broken to support:  I've had a good try at getting things working in https://github.com/boostorg/math/pull/602, but every time you fix one issue something else pops up.  Currently stalled because `is_complex_type<>` fails completely with these compilers.  That's fixable, but I suspect other issues will follow.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-04-07 17:46:12 UTC  
> Url: https://github.com/boostorg/math/pull/601#issuecomment-815103097  

> I'm beginning to think that gcc-4.8 and 4.9...  
  
Personally, I draw the line at 5, preferably 5.2, which features decent C++11 support. As it turns out, I use quite a few embedded compilers around that 5 zone, and even one single exotic architecture with a 4.9.  
  
But I have found it very difficult to roll today's code, the way we write it in modern C++11 prior to the GCC5 barrier.

---

## Comment 6

> Username: mborland  
> Created_at: 2021-04-07 17:50:53 UTC  
> Url: https://github.com/boostorg/math/pull/601#issuecomment-815106057  

> > I'm beginning to think that gcc-4.8 and 4.9...  
>   
> Personally, I draw the line at 5, preferably 5.2, which features decent C++11 support. As it turns out, I use quite a few embedded compilers around that 5 zone, and even one single exotic architecture with a 4.9.  
>   
> But I have found it very difficult to roll today's code, the way we write it in modern C++11 prior to the GCC5 barrier.  
  
  
I agree with @ckormanyos. I think the only real workaround for GCC 5/6 was in `is_complex_type`.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-04-11 12:41:59 UTC  
> Url: https://github.com/boostorg/math/pull/601#issuecomment-817301551  

Closing, gcc < 5 is a lost cause for us now.

---
