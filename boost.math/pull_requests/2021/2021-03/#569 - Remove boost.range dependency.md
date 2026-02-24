# #569 Remove boost.range dependency [Merged]

> Username: mborland  
> Created at: 2021-03-14 07:25:05 UTC  
> Updated at: 2021-03-22 17:26:07 UTC  
> Merged at: 2021-03-20 19:40:52 UTC  
> Closed at: 2021-03-20 19:40:53 UTC  
> Url: https://github.com/boostorg/math/pull/569  



---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2021-03-14 13:11:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/569#pullrequestreview-611694547  

📁 include/boost/math/distributions/hyperexponential.hpp

```diff
 279 |     // probably an iterator:
 285 |-     public: template <typename ProbRangeT, typename RateRangeT>
 280 |+     public: template <typename ProbRangeT, typename RateRangeT,
```

> Username: jzmaddock  
> Created_at: 2021-03-14 13:11:35 UTC  
> Updated_at: 2021-03-20 07:49:08 UTC  
> Url: https://github.com/boostorg/math/pull/569#discussion_r593898453  

Sorry Matt, but this is going to be a lot more complex: we have two sets of 2-arg constructors, one accepts 2 ranges, conceptually that's anything that has begin() and end() member functions, but now that C++20 is here, we should probably take the opportunity to check that we're compatible with native range concepts.  The other constructor accepts 2 iterators.  So the use of has_pre_incremement is a simplistic way to check for an iterator concept, and I'm afraid is_pointer doesn't match this.  
  
BTW the "modern" way to implement concept-like-traits is via is_detected: https://www.boost.org/doc/libs/1_75_0/libs/type_traits/doc/html/boost_typetraits/reference/is_detected.html  GCC has it's own version but it's not in the std sadly so other compilers do not :(

> Username: mborland  
> Created_at: 2021-03-14 13:24:43 UTC  
> Updated_at: 2021-03-20 07:49:08 UTC  
> Url: https://github.com/boostorg/math/pull/569#discussion_r593900199  

I saw the comment about using a pointer and was hopeful that would work. `std::iterator_traits` is only SFINAE friendly in C++14 and on or that would be the easy button. I'll have to take a look at your link and come back with a more robust solution.


---

## Comment 2

> Username: mborland  
> Created_at: 2021-03-15 19:45:02 UTC  
> Url: https://github.com/boostorg/math/pull/569#issuecomment-799702864  

Using SFINAE on the minimum requirements for an iterator (pre-increment and derefernce) seems to have solved the issue from using `is_pointer`. I also added tests for using `std::ranges::begin/end` and a native range; both pass locally without needing any additional constructors.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-03-16 08:39:08 UTC  
> Url: https://github.com/boostorg/math/pull/569#issuecomment-800067143  

> I also added tests for using std::ranges::begin/end and a native range; both pass locally without needing any additional constructors.  
  
Cool!

---

## Comment 4

> Username: mborland  
> Created_at: 2021-03-20 17:14:33 UTC  
> Url: https://github.com/boostorg/math/pull/569#issuecomment-803428455  

This is good to go. I added an implementation of `is_detected` like you recommended; it will get further use removing boost.type_traits.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-03-22 09:08:16 UTC  
> Url: https://github.com/boostorg/math/pull/569#issuecomment-803895131  

That's weird, merging this has triggered a new failure in univariate_statistics_test: https://github.com/boostorg/math/runs/2162144720?check_suite_focus=true

---

## Comment 6

> Username: mborland  
> Created_at: 2021-03-22 15:37:15 UTC  
> Url: https://github.com/boostorg/math/pull/569#issuecomment-804159349  

Maybe a spurious failure? Nothing here would have affected gini coefficient calculations.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2021-03-22 17:26:07 UTC  
> Url: https://github.com/boostorg/math/pull/569#issuecomment-804251406  

>Maybe a spurious failure? Nothing here would have affected gini coefficient calculations.  
  
No.  I worry more that we have a non-deterministic test which sometimes passes and sometimes fails?

---
