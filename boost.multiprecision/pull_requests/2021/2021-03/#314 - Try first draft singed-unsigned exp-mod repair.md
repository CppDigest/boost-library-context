# #314 Try first draft singed/unsigned exp/mod repair [Merged]

> Username: ckormanyos  
> Created at: 2021-03-31 09:12:12 UTC  
> Updated at: 2021-04-04 16:50:08 UTC  
> Merged at: 2021-04-03 19:22:38 UTC  
> Closed at: 2021-04-03 19:22:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314  

Try using `ptrdiff_t` instead of `size_t`. TBD: If CI OK, then will also add a specific test case

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-03-31 09:38:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-810927394  

Hi @jzmaddock as we progress on this, could we add some `-fsanitize=undefined` runs on either GCC, clang or a few combinations of  both?  
  
I can't find out exactly how/if you put any sanitizers in circle or drone. I thought you had, but I'm not recalling where or how...

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-03-31 14:27:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-811111974  

While testing in CI I note that it appears as though the Circle CI YAML might have some typos regarding the test run name.  
  
The phrase _Building MPFR Tests_ is used in some places where it might not belong. See pic below and, for instance, [this line](https://github.com/boostorg/multiprecision/blob/95a9bf52e3325a37633bb7ad8d3eee9faf392385/.circleci/config.yml#L187)  
  
I will fix these in the context of this PR in the next push  
  
![grafik](https://user-images.githubusercontent.com/2404721/113160470-d1799a80-923d-11eb-8f34-6797a5f6629e.png)

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-03-31 14:36:28 UTC  
> Updated_at: 2021-03-31 14:37:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-811118595  

Hi @jzmaddock in CI running at the moment is a somewhat new idea in `cpp_dec_float`.  
  
This idea is quite closely related to #313, close enough for me to try out potential simplification(s).  
  
Long have I noted the plethora of string constructors in the function `pow2`. I have added a new constructor from `initializer_list` and shown its potential use in a [new form for pow2 here](https://github.com/boostorg/multiprecision/blob/e1fbbfaebf00ab1e413343861dd657a2633c0bc8/include/boost/multiprecision/cpp_dec_float.hpp#L2371).  
  
At the moment, I am only using 9 table entries instead of the familiar 256. I wonder what you think, in particular of the ctor from `initializer_list`? In the future, this ctor could also facilitate `constexpr` construction as soon as the underlying data containers are adapted accordingly.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-04-01 10:43:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-811821899  

It appears as though the failing tests in the CI builds/runs are exclusively on OSX 7.x, which is seemingly not aware of the attribute `thread_local`. I think this needs to be changed in Boost.Math.  
  
Something like in `config.hpp`, to ensure that `thread_local` is not attempted to be used for `(__clang__major__ <= 7)`, or similar such check.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2021-04-01 11:01:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-811830801  

>At the moment, I am only using 9 table entries instead of the familiar 256. I wonder what you think, in particular of the ctor from initializer_list? In the future, this ctor could also facilitate constexpr construction as soon as the underlying data containers are adapted accordingly.  
  
Looks good to me!

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2021-04-01 11:42:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-811850026  

>It appears as though the failing tests in the CI builds/runs are exclusively on OSX 7.x, which is seemingly not aware of the attribute thread_local. I think this needs to be changed in Boost.Math.  
  
Ah... I think this was a deliberate change to drop support for non-C++-11 compilers, XCode-7 is pretty old now, should we care about this does everyone use the latest XCode release?

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2021-04-01 11:54:56 UTC  
> Updated_at: 2021-04-01 11:55:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-811855681  

> a deliberate change to drop support for non-C++-11 compilers, XCode-7 is pretty old now  
  
On the one hand, it looked like failures only for the attribute `thread_local`, which that compiler did not support. On the other hand, I belive we're talking about that 5 - 6 years old timing now on that tool chain. I personally can live without supporting it in CI, or at all for that matter. In fact, I almost edited the drone scripts to get rid of it in the branch just to get my own stuff green.  
  
My vote would be to drop it. If that turns out to be too radical, we would need to see how to blend out `thread_local` and still get green on it, which might be simple or difficult, I can't judge that in the moment. But it looked like those were the only failing tests, the ones involving that attribute.

---

## Comment 8

> Username: NAThompson  
> Created_at: 2021-04-01 12:28:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-811872367  

> Ah... I think this was a deliberate change to drop support for non-C++-11 compilers, XCode-7 is pretty old now, should we care about this does everyone use the latest XCode release?  
  
Yeah everyone uses the latest. I'd get rid of it.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-04-03 05:23:15 UTC  
> Updated_at: 2021-04-03 05:24:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-812814433  

This PR is now ready for review.  
  
The fix addresses the original issue, by:  
- changing the [signedness of the exp/mod](https://github.com/boostorg/multiprecision/blob/26f704dd1ec554d3a7dfd9b286cf13fc22502c85/include/boost/multiprecision/cpp_dec_float.hpp#L2019) calculation  
- adding a [private constructor](https://github.com/boostorg/multiprecision/blob/26f704dd1ec554d3a7dfd9b286cf13fc22502c85/include/boost/multiprecision/cpp_dec_float.hpp#L559) from `std::initializer_list` which [subsequently simplifies `pow2`](https://github.com/boostorg/multiprecision/blob/26f704dd1ec554d3a7dfd9b286cf13fc22502c85/include/boost/multiprecision/cpp_dec_float.hpp#L2378) as well as other `static const` values.  
- adding [test code exercising the original issue](https://github.com/boostorg/multiprecision/blob/ubsan_size_t_exp_issue313/test/git_issue_313.cpp)  
- adding some [`-fsanitize=undefined`](https://github.com/boostorg/multiprecision/blob/26f704dd1ec554d3a7dfd9b286cf13fc22502c85/.github/workflows/multiprecision.yml#L142) runs in CI on GHA  
- fixing CI run names  
  
The scope of change was a bit larger than initially anticipated, but seems to go in the direction of fixing this issue and also simplifying the ovreall `cpp_dec-float.hpp`.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2021-04-03 10:09:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-812844220  

Looks good to me Chris - do you want to go ahead and merge when you're ready?  
  
BTW the original issue was triggered by -fsanitize=integer.  However, adding that would trigger countless errors in our dependencies :(

---

## Comment 11

> Username: ckormanyos  
> Created_at: 2021-04-03 19:22:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-812913909  

> go ahead and merge when you're ready?  
  
Yes. Done. Thanks for checking this.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2021-04-03 21:20:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/314#issuecomment-812927117  

Fixes #313 (in the Multiprecision part)

---
