# #531 Merge Boost.StaticAssert into Boost.Config. [Merged]

> Username: Lastique  
> Created at: 2025-12-11 22:48:55 UTC  
> Updated at: 2025-12-27 14:29:59 UTC  
> Merged at: 2025-12-26 15:46:14 UTC  
> Closed at: 2025-12-26 15:46:14 UTC  
> Url: https://github.com/boostorg/config/pull/531  

This follows [this](https://lists.boost.org/archives/list/boost@lists.boost.org/thread/TWKBI5NXF772FGOBZA2FLIC2S7AM6OCQ/#TWKBI5NXF772FGOBZA2FLIC2S7AM6OCQ) discussion on the Boost ML.  
  
This merges the sources, documentation, tests and examples of Boost.StaticAssert into Boost.Config. Documentation has been updated to be a part of Boost.Config documentation. One other change is use a more specific include from Boost.TypeTraits in static_assert_example_2. The rest is unchanged and at the state of Boost 1.90.0 release (https://github.com/boostorg/static_assert/tree/boost-1.90.0).  
  
The test/cmake_install_test and test/cmake_subdir_test directories from Boost.StaticAssert are not included in this PR. These tests were not enabled since Travis CI went down and are not applicable to the merged Boost.StaticAssert as these tests check for whether Boost.StaticAssert is usable from CMake. Similar tests could be written for Boost.Config, but that would have to be a separate work.  
  
This PR must be merged together with https://github.com/boostorg/static_assert/pull/18

---

## Comment 1

> Username: Lastique  
> Created_at: 2025-12-11 22:53:52 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3644123465  

Note: the CI is failing because the boost/static_assert.hpp header exists both in this PR and in develop of Boost.StaticAssert. This will be resolved when Boost.StaticAssert PR is merged that will remove the header from there.

---

## Comment 2

> Username: Lastique  
> Created_at: 2025-12-11 22:56:52 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3644133913  

@pdimov, please see if this works for you. And perhaps you could coordinate the merge of the two PRs together.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-12-12 09:37:05 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3645692479  

This doesn't retain the history of `boost/static_assert.hpp`. I'm not sure if we need to care about that, though.

---

## Comment 4

> Username: Lastique  
> Created_at: 2025-12-12 12:04:49 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3646214996  

I'll see if I can extract with history, but I'm not sure it'll work if there are commits that touch paths that are present in Boost.Config.

---

## Comment 5

> Username: Lastique  
> Created_at: 2025-12-12 23:31:50 UTC  
> Updated_at: 2025-12-12 23:33:25 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3648505810  

I've added Boost.StaticAssert history. Commit hashes will be different from the original git repo since the history has been filtered from the bits that are not included in this merge.  
  
Also, I trimmed trailing spaces and newlines in Boost.StaticAssert sources (as a separate commit).

---

## Comment 6

> Username: pdimov  
> Created_at: 2025-12-14 20:29:09 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3652002370  

I think we should delay this until the b2 msvc-14.5 update (hopefully soon) and the associated changes here in Config.

---

## Comment 7

> Username: Lastique  
> Created_at: 2025-12-14 21:27:11 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3652180444  

Ok. Let me know if and when I need to do anything here.

---

## Comment 8

> Username: Lastique  
> Created_at: 2025-12-25 22:00:12 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3691772218  

Is it time to merge this now?

---

## Comment 9

> Username: pdimov  
> Created_at: 2025-12-26 01:12:36 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3691882700  

It is. I'll do it tomorrow.

---

## Comment 10

> Username: Lastique  
> Created_at: 2025-12-26 16:03:51 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3693053774  

Thank you.

---

## Comment 11

> Username: pdimov  
> Created_at: 2025-12-27 01:07:21 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3693538458  

Once the dust caused by the move settles, should we make BOOST_STATIC_ASSERT available from boost/config.hpp? It would be consistent with the other support macros defined by Boost.Config.  
  
Maybe move the header to boost/config/static_assert.hpp and make boost/static_assert.hpp include it?

---

## Comment 12

> Username: Lastique  
> Created_at: 2025-12-27 09:54:38 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3693873916  

There's quite a bit of code in static_assert.hpp, and not everyone needs it. Eventually, the number of users will decrease as people switch to core `static_assert`. So I think it is better to stay separate, but I don't have a strong opinion on this.

---

## Comment 13

> Username: pdimov  
> Created_at: 2025-12-27 14:29:59 UTC  
> Url: https://github.com/boostorg/config/pull/531#issuecomment-3694012399  

Neither do I, to be honest. Most of the code is actually ifdef-ed out, but there's no pressing need to make any changes.

---
