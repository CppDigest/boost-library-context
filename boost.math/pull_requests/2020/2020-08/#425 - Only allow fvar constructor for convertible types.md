# #425 Only allow fvar constructor for convertible types [Open]

> Username: wgledbetter  
> Created at: 2020-08-26 03:59:27 UTC  
> Updated at: 2022-10-29 17:13:15 UTC  
> Url: https://github.com/boostorg/math/pull/425  

#424   
  
Use SFINAE to enable copy construction and assignment only from valid types. Allows use in Eigen library.  
  
`test_autodiff` 1, 2, and 3 succeed, but number 4 fails due to `boost::has_right_shift` required by `boost::lexical_cast`.

---

## Comment 1

> Username: wgledbetter  
> Created_at: 2020-08-26 16:27:03 UTC  
> Url: https://github.com/boostorg/math/pull/425#issuecomment-680986277  

Added overload of `operator>>` that behaves like `operator=` to solve issue with `has_right_shift`.

---

## Review 2 [Approved]

> Username: pulver  
> Created_at: 2020-08-27 12:16:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/425#pullrequestreview-476660205  

This change looks like a good improvement in general compatibility, with minimal changes. Really appreciate the contribution @wgledbetter.  
  
@NAThompson any comments before merging?  
  
The [AppVeyor build issues](https://ci.appveyor.com/project/jzmaddock/math/builds/34867405) are pre-existing and seem to be independent of this change.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-08-27 13:15:43 UTC  
> Url: https://github.com/boostorg/math/pull/425#issuecomment-681941975  

The build failures do look like a pre-existing problem, but unfortunately I don't have time to investigate; have they failed in any other builds?  
  
I have read the diffs and think they look good.  
  
I'm going to let @jzmaddock squash merge this since he understands `lexical_cast` much better than I do.

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-08-27 13:16:59 UTC  
> Updated_at: 2020-08-27 13:18:53 UTC  
> Url: https://github.com/boostorg/math/pull/425#issuecomment-681942719  

@pulver : How do I get the cool "Approved" button you put on the PR? (This is the sort of thing I have time for!)

---

## Comment 5

> Username: pulver  
> Created_at: 2020-08-27 13:38:12 UTC  
> Url: https://github.com/boostorg/math/pull/425#issuecomment-681954443  

> have they failed in any other builds?  
  
Yes: https://ci.appveyor.com/project/jzmaddock/math/builds/34818710  
This is from a few days ago prior to any commits related to this PR.  
  
> How do I get the cool "Approved" button you put on the PR?  
  
`Files changed` tab > green `Review changes` button > `Approve` radio button.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2020-08-27 16:40:44 UTC  
> Url: https://github.com/boostorg/math/pull/425#issuecomment-682062101  

Looks good to me too - do we have a test case for the original Eigen issue?  We do have some Eigen interoperability tests in Multiprecision which could be copied here (which is to say I'll sort out the Jamfile stuff if you don't want to go there).  
  
I'll try to sort out the Appveyor issue too - we really need to get that fixed - not really sure how it crept in to be honest.

---

## Comment 7

> Username: jzmaddock  
> Created_at: 2020-08-27 17:37:44 UTC  
> Url: https://github.com/boostorg/math/pull/425#issuecomment-682092038  

I'm hoping this will fix the outstanding develop test failure: https://github.com/boostorg/math/commit/526fc6eef47cbdedc09d0895c9ce97ed383dc45a

---

## Comment 8

> Username: wgledbetter  
> Created_at: 2020-08-27 21:10:55 UTC  
> Url: https://github.com/boostorg/math/pull/425#issuecomment-682192795  

@NAThompson and @pulver Thanks for the quick review!  
  
@jzmaddock I'll work on some Eigen tests over the weekend if you can help me put them into the existing test framework.

---

## Comment 9

> Username: NAThompson  
> Created_at: 2020-09-01 18:31:23 UTC  
> Url: https://github.com/boostorg/math/pull/425#issuecomment-685056469  

So there is a broader question now: This now explicitly uses Eigen in the boost source directory, but traditionally we only test that our code is "generic enough" to interoperate with Eigen.  
  
Do we have a compelling reason to use Eigen explicitly, rather than (say) writing generic code that will work with Eigen and Boost.ublas?

---

## Comment 10

> Username: wgledbetter  
> Created_at: 2020-09-01 19:06:13 UTC  
> Url: https://github.com/boostorg/math/pull/425#issuecomment-685073779  

@NAThompson boost multiprecision has an eigen.hpp compatibility file that directly includes Eigen, and I was referencing that code for the recent updates. It provides an overload of Eigen::NumTraits that is necessary for full compatibility. The other overload it provides is Eigen::ScalarBinaryOpTraits. This one is proving troublesome because the libeigen3-dev package on travis:xenial is out of date. That reason alone is probably enough to remove the Eigen include, since end-users may still be using the older version.  
  
The commit I'm about to push relocates the Eigen code from the source directory to the test directory.

---
