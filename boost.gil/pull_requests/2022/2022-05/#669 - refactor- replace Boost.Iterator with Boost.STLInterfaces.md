# #669 refactor: replace Boost.Iterator with Boost.STLInterfaces [Open]

> Username: sdebionne  
> Created at: 2022-05-12 09:52:26 UTC  
> Updated at: 2025-11-26 15:34:23 UTC  
> Url: https://github.com/boostorg/gil/pull/669  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Contribute to remove dependencies to older c++03 boost libraries.  
  
### References  
  
[C++11 Modernization ](https://github.com/boostorg/gil/projects/3)  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [x] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: sdebionne  
> Created_at: 2022-05-12 11:04:51 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1124856557  

@mloskot Boost.Iterator provides it's own Concept checking classes in `#include <boost/iterator/iterator_concepts.hpp>` that matche the (new for the time) Boost.Iterator concepts that never made it to the standard. Since we want to cut the ties with Boost.Iterator, I suppose we need to move to [Boost.Concept Iterator concept](https://www.boost.org/doc/libs/1_79_0/libs/concept_check/reference.htm#iterator-concepts), what do you think?  
  
Honestly I wonder if it is worth it, and if, _for concept checking only_, we could require c++20....

---

## Comment 2

> Username: mloskot  
> Created_at: 2022-05-12 18:58:47 UTC  
> Updated_at: 2022-05-13 21:07:23 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1125323965  

@sdebionne   
> Honestly I wonder if it is worth it, and if, for concept checking only, we could require c++20...  
  
I don't think it is worth it ...  
  
> to move to [Boost.Concept Iterator concept](https://www.boost.org/doc/libs/1_79_0/libs/concept_check/reference.htm#iterator-concepts),  
  
One of my "next big thing" to do for GIL is to:  
1. Freeze current use of Boost.Concept  
2. Introduce C++20 concepts (compile-time disabled for older compilation modes)  
3. Ensure we CI-test at C++20 level thoroughly (core + extensions) with those concepts enabled.  
4. Slowly deprecated use of Boost.Concept, and eventually remove it.  
  
@sdebionne Does the above seem sensible to you?

---

## Comment 3

> Username: mloskot  
> Created_at: 2022-05-20 09:15:35 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1132671610  

This is included in the planning towards C++14/17 discussion here https://github.com/boostorg/gil/discussions/676

---

## Comment 4

> Username: mloskot  
> Created_at: 2022-06-25 14:20:42 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1166297724  

I guess we may run out of time to squeeze it into Boost 1.80. What do you think @sdebionne ?

---

## Comment 5

> Username: sdebionne  
> Created_at: 2022-06-27 08:26:45 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1167042825  

> I guess we may run out of time to squeeze it into Boost 1.80. What do you think @sdebionne ?  
  
Indeed, I underestimate the work and overestimate the time I could spend on the project. Let's move it to 1.81.

---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2023-09-26 13:01:52 UTC  
> Updated_at: 2025-11-26 12:22:33 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1735499860  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/669?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
:x: Patch coverage is `97.29730%` with `1 line` in your changes missing coverage. Please review.  
:white_check_mark: Project coverage is 81.99%. Comparing base ([`82fb089`](https://app.codecov.io/gh/boostorg/gil/commit/82fb0899dd0145efe61b15838439034ef4c26400?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)) to head ([`bc65b38`](https://app.codecov.io/gh/boostorg/gil/commit/bc65b38ca11b0f4a0643bf6970cb69cc568d8a30?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)).  
:warning: Report is 1 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #669   +/-   ##  
========================================  
  Coverage    81.99%   81.99%             
========================================  
  Files          117      117             
  Lines         5354     5361    +7       
========================================  
+ Hits          4390     4396    +6       
- Misses         964      965    +1       
```  
</details>  
<details><summary> :rocket: New features to boost your workflow: </summary>  
  
- :snowflake: [Test Analytics](https://docs.codecov.com/docs/test-analytics): Detect flaky tests, report on failures, and find test suite problems.  
</details>

---

## Comment 7

> Username: sdebionne  
> Created_at: 2023-09-28 18:38:29 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1739828056  

@mloskot there is still a bit of cleaning to do but otherwise this PR is ready for review. Beside the switch to Boost.StlInferface, there is a fix for the CI (code coverage), and a fix for `std::is_floating_point<>` specialization.  
  
I don't known why some of the CI GitHub actions timeout -I have just try to rerun them.

---

## Comment 8

> Username: mloskot  
> Created_at: 2023-09-30 09:41:06 UTC  
> Updated_at: 2023-09-30 09:52:46 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1741726892  

> I don't known why some of the CI GitHub actions timeout -I have just try to rerun them.  
  
It looks like GHA runners availability issue which I'd ignore:  
  
![image](https://github.com/boostorg/gil/assets/80741/89ece95c-eeb6-4ff7-be5a-74fe46c25403)  
  
The Ubuntu 18.04 has also been deprecated, so I have just tried to update the GHA:  
  
- https://github.com/boostorg/gil/pull/738  
  
Let's see if this improves anything.

---

## Review 9 [Commented]

> Username: mloskot  
> Created_at: 2023-09-30 09:54:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/669#pullrequestreview-1651671649  

📁 example/interleaved_ptr.hpp

```diff
  79 |+     ///// For some reason operator[] provided by boost::iterator_facade returns a custom class that is convertible to reference
  80 |+     ///// We require our own reference because it is registered in iterator_traits
  81 |+     //reference operator[](difference_type d) const { return memunit_advanced_ref(*this,d*sizeof(channel_t));}
```

> Username: mloskot  
> Created_at: 2023-09-30 09:54:05 UTC  
> Url: https://github.com/boostorg/gil/pull/669#discussion_r1341938978  

I assume this is going to be part of the clean up.

> Username: sdebionne  
> Created_at: 2023-10-02 07:08:32 UTC  
> Url: https://github.com/boostorg/gil/pull/669#discussion_r1342340665  

Absolutely!


---

## Review 10 [Commented]

> Username: mloskot  
> Created_at: 2023-09-30 09:56:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/669#pullrequestreview-1651671880  

📁 include/boost/gil/algorithm.hpp

```diff
 191 | struct copier_n {
 192 |-     BOOST_FORCEINLINE void operator()(I src, typename std::iterator_traits<I>::difference_type n, O dst) const { std::copy(src,src+n, dst); }
 192 |+     BOOST_FORCEINLINE void operator()(I src, typename std::iterator_traits<I>::difference_type n, O dst) const { BOOST_ASSERT(n >= 0);  std::copy(src, src + n, dst); }
```

> Username: mloskot  
> Created_at: 2023-09-30 09:56:38 UTC  
> Url: https://github.com/boostorg/gil/pull/669#discussion_r1341939183  

The use of assertions is fine, I think, since it's `detail`, so called internally.  
  
I just still remember discussions about using assertions on arguments of public functions:  
- https://github.com/boostorg/gil/issues/360

> Username: sdebionne  
> Created_at: 2023-10-02 07:16:34 UTC  
> Updated_at: 2023-10-02 07:16:35 UTC  
> Url: https://github.com/boostorg/gil/pull/669#discussion_r1342346405  

> I believe that one should never fail an assert on a runtime condition, like e.g. zero-sized file, file reading error, disk full, or such.  
  
I agreee with this policy. In our case, the doc says for `std::copy_n`:  
  
> Zero assignments if count < 0; count assignments otherwise.  
  
so we should not assert but return early. Event if having `count < 0` is probably a programming mistake. I'll fix that.


---

## Review 11 [Commented]

> Username: mloskot  
> Created_at: 2023-09-30 09:57:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/669#pullrequestreview-1651671973  

📁 include/boost/gil/concepts/pixel_iterator.hpp

```diff
  48 |+ //struct ForwardIteratorIsMutableConcept
  49 |+ //{
  50 |+ //    void constraints()
```

> Username: mloskot  
> Created_at: 2023-09-30 09:57:22 UTC  
> Url: https://github.com/boostorg/gil/pull/669#discussion_r1341939242  

I dream about switching over to C++20 concepts :-)

> Username: sdebionne  
> Created_at: 2023-10-02 07:18:00 UTC  
> Url: https://github.com/boostorg/gil/pull/669#discussion_r1342347406  

Baby steps... But I agree that moving to C++20, at least for concept checking, would be ideal.


---

## Review 12 [Approved]

> Username: mloskot  
> Created_at: 2023-09-30 09:59:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/669#pullrequestreview-1651672242  

Thank you so much for pulling this off @sdebionne

---

## Comment 13

> Username: sdebionne  
> Created_at: 2023-10-02 07:18:43 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1742512105  

I'll rebase on the fixed CI (thank you for that) and cleanup.

---

## Comment 14

> Username: sdebionne  
> Created_at: 2023-10-03 07:05:58 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1744330376  

I wonder if `iterator` should be removed from `.ci/get-boost.sh`?

---

## Comment 15

> Username: mloskot  
> Created_at: 2023-10-03 09:15:49 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-1744563550  

Yes, I think it should.  
  
It may be required as transitive dependency, but the CI jobs will let us know.

---

## Comment 16

> Username: sdebionne  
> Created_at: 2025-11-26 15:34:23 UTC  
> Url: https://github.com/boostorg/gil/pull/669#issuecomment-3581888910  

Concept checking errors with `stdcxx=20` to be dealt with:  
  
```  
./boost/concept_check.hpp:209:13: error: conversion from ‘std::input_iterator_tag’ to non-scalar type ‘std::bidirectional_iterator_tag’ requested  
```

---
