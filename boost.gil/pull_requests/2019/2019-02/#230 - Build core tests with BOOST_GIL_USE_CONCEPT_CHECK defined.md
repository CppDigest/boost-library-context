# #230 Build core tests with BOOST_GIL_USE_CONCEPT_CHECK defined [Merged]

> Username: mloskot  
> Created at: 2019-02-03 00:31:19 UTC  
> Updated at: 2019-02-05 22:25:14 UTC  
> Merged at: 2019-02-05 22:19:36 UTC  
> Closed at: 2019-02-05 22:19:36 UTC  
> Url: https://github.com/boostorg/gil/pull/230  

when building tests with both, Boost.Build and CMake.  
  
Disable concepts check for tests where range (e.g. `std::array`)  
used as image pixel - not fully specialised as acceptable pixel type.  
  
Tests of extensions will require more work (e.g. numeric test does not compile).  
  
### References  
  
Closes #228  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-03 00:48:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/230#pullrequestreview-199367948  

📁 test/algorithm/std_fill.cpp

```diff
   7 | //
   8 |+ #ifdef BOOST_GIL_USE_CONCEPT_CHECK
   9 |+ // FIXME: Range as pixel does not seem to fulfil pixel concepts due to no specializations required:
```

> Username: stefanseefeld  
> Created_at: 2019-02-03 00:48:36 UTC  
> Updated_at: 2019-02-05 20:42:58 UTC  
> Url: https://github.com/boostorg/gil/pull/230#discussion_r253283397  

s/fulfil/fullfill/ :-)

> Username: mloskot  
> Created_at: 2019-02-03 01:18:21 UTC  
> Updated_at: 2019-02-05 20:42:58 UTC  
> Url: https://github.com/boostorg/gil/pull/230#discussion_r253283925  

Hmm, https://tfd.com/fulfil  
  
I'm British English biased though :)

> Username: stefanseefeld  
> Created_at: 2019-02-03 18:48:12 UTC  
> Updated_at: 2019-02-05 20:42:58 UTC  
> Url: https://github.com/boostorg/gil/pull/230#discussion_r253314167  

Hmm, ok, point taken. :-)

> Username: mloskot  
> Created_at: 2019-02-03 22:32:59 UTC  
> Updated_at: 2019-02-05 20:42:58 UTC  
> Url: https://github.com/boostorg/gil/pull/230#discussion_r253323042  

Lets go for a compromise, https://en.oxforddictionaries.com/definition/fulfill :)


---

## Comment 2

> Username: mloskot  
> Created_at: 2019-02-03 22:54:20 UTC  
> Url: https://github.com/boostorg/gil/pull/230#issuecomment-460096863  

I've updated the PR to enable concept check for core tests only (high priority).  
The extensions tests are/may be failing - fixes required but with lower priority than the core.

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-02-03 23:26:32 UTC  
> Url: https://github.com/boostorg/gil/pull/230#issuecomment-460099039  

Some Travis CI jobs are failing with, https://travis-ci.org/boostorg/gil/jobs/488276370  
  
```  
The job exceeded the maximum log length, and has been terminated.  
```  
  
Joy!

---

## Comment 4

> Username: stefanseefeld  
> Created_at: 2019-02-04 00:30:38 UTC  
> Url: https://github.com/boostorg/gil/pull/230#issuecomment-460103607  

Yeah. It looks like `-Wfloat-equal` is causing a ton of warnings inside the concept checks. I suggest disabling them (temporarily) with pragmas, and then patching the code by replacing `operator==` and `operator!=` by some robust constexpr `equal` function ?

---

## Comment 5

> Username: mloskot  
> Created_at: 2019-02-04 22:18:15 UTC  
> Url: https://github.com/boostorg/gil/pull/230#issuecomment-460435681  

@stefanseefeld   
> I suggest disabling them (temporarily) with pragmas,   
  
Done  
  
> and then patching the code by replacing operator== and operator!= by some robust constexpr equal function ?  
  
I don't think we can do anything other than ignoring this  
  
```  
./boost/concept_check.hpp:235:30: warning: comparing floating point with == or != is unsafe [-Wfloat-equal]  
      require_boolean_expr(a != b);  
                           ~ ^  ~  
```  
  
I've ignored `-Wfloat-equal` in `test/channel.cpp` code in https://github.com/boostorg/gil/commit/2dc8ad8fac0a390d2e2e68aee8e6e4f4b09a7c3c  
  
Regarding some robust float-point comparison functions for general case in GIL, I have been thinking about it (there are those FP comparison utilities [in Boost.Math](https://www.boost.org/doc/libs/1_69_0/libs/math/doc/html/math_toolkit/float_comparison.html) and [in Boost.Test](https://www.boost.org/doc/libs/1_69_0/libs/test/doc/html/boost_test/testing_tools/extended_comparison/floating_point.html), but that w/could change performance of GIL where original authors _seem_ to deliberately avoided more complex comparators. I don't know what was the actual rationale, but this topic may be worth to discuss at some point.

---

## Comment 6

> Username: mloskot  
> Created_at: 2019-02-05 00:16:22 UTC  
> Updated_at: 2019-02-05 00:18:03 UTC  
> Url: https://github.com/boostorg/gil/pull/230#issuecomment-460465251  

There are still too many warnings issued from concepts check, amongst them flowers like this one (or line of code savers?)   
  
```  
./boost/gil/concepts/pixel_iterator.hpp:60:11: warning: unsequenced modification and access to 'i' [-Wunsequenced]  
        *i-- = *i; // require postdecrement and assignment  
          ^     ~  
```

---

## Comment 7

> Username: mloskot  
> Created_at: 2019-02-05 22:25:14 UTC  
> Url: https://github.com/boostorg/gil/pull/230#issuecomment-460828462  

It's possible this fixed #174 and related issue linked there

---
