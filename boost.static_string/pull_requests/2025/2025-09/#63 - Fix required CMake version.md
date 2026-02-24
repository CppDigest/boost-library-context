# #63 Fix required CMake version [Merged]

> Username: Flamefire  
> Created at: 2025-09-30 17:37:34 UTC  
> Updated at: 2025-10-24 11:46:24 UTC  
> Merged at: 2025-10-24 09:58:47 UTC  
> Closed at: 2025-10-24 09:58:47 UTC  
> Url: https://github.com/boostorg/static_string/pull/63  

`source_group(TREE` requires CMake 3.8

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2025-09-30 23:40:53 UTC  
> Url: https://github.com/boostorg/static_string/pull/63#issuecomment-3354132827  

CI did not like this !

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-01 07:18:01 UTC  
> Updated_at: 2025-10-01 07:22:39 UTC  
> Url: https://github.com/boostorg/static_string/pull/63#issuecomment-3355058179  

CI seems to be broken unrelated to this change. It doesn't even have the workaround for the node issue that most other libraries have by now.  
  
It would be good to move this to the Boost.CI reusable workflow, see #64 and fix the tests broken on C++2c

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2025-10-10 00:48:09 UTC  
> Url: https://github.com/boostorg/static_string/pull/63#issuecomment-3387895067  

If there is a more modern CI workflow, yes. Is this the one that Alan wrote all the scripts for?

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-10-10 07:00:54 UTC  
> Url: https://github.com/boostorg/static_string/pull/63#issuecomment-3388559052  

The switch now happened with https://github.com/boostorg/static_string/pull/64  
  
It is using the scripts from the Boost.CI repository and the commonized workflow written by James such that updates can happen in a central place instead of having to duplicate changes over all repositories.  
  
Why scripts did you refer too?

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2025-10-12 12:05:56 UTC  
> Url: https://github.com/boostorg/static_string/pull/63#issuecomment-3394267871  

@alandefreitas wrote a pretty beefy set of things for GHA. Not sure how it ties in to Boost.CI. Alan?

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2025-10-14 03:42:55 UTC  
> Url: https://github.com/boostorg/static_string/pull/63#issuecomment-3399994993  

> Alan?  
  
My cpp-actions project provides individual steps with GHA that you can combine to form a generic workflow. Since then, boost-ci has started providing a more fixed, [reusable workflow](https://github.com/boostorg/boost-ci/blob/master/.github/workflows/reusable.yml) for boost libraries. It's a trade-off that depends on whether your library has special variants that need to be tested.  
  
Boost.StaticString moved to this boost-ci reusable work (#64), and I believe it's appropriate for this project. My only concern is that the reusable workflow can't be adapted, so I'm not sure the it properly tests the standalone variants. But I imagine whoever planned and approved this transition has already considered this issue.

---
