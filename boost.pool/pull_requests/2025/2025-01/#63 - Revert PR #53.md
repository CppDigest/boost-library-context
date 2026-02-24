# #63 Revert PR #53 [Closed]

> Username: ni-vzavalishin  
> Created at: 2025-01-15 14:04:53 UTC  
> Updated at: 2025-02-25 19:23:00 UTC  
> Closed at: 2025-02-25 19:23:00 UTC  
> Url: https://github.com/boostorg/pool/pull/63  

It looks like [PR #53](https://github.com/boostorg/pool/pull/53) has introduced a regression into `boost pool`. The present PR essentially reverts PR #53, leaving some of the changes. It also contains new unit tests demonstrating the regression.  
  
**TL;DR**: `simple_segregated_storage` in its present form is fundamentally uncapable of handling mixed partition sizes, and cannot be "fixed" to allow that handling without a major change of the implementation. The linked-to [Example 4.1](https://theboostcpplibraries.com/boost.pool#ex.pool_01), which was a starting point for the "fix", looks simply wrong and so does the fix itself.  
  
**The rationale for #53 being a regression is as follows.**  
  
PR #53 purports to fix the issue of `simple_segregated_storage` not being able to correctly handle varying values of `partition_sz`. However, upon inspecting the code of `simple_segregated_storage`, it looks like the latter is not designed to simultaneously handle different values of `partition_sz` at all. Rather one and the same value is supposed to be stored externally and consistently supplied to the calls into one and the same `simple_segregated_storage` instance.  
  
Let's go into detail of why the latter is the case. A `simple_segregated_storage` keeps an ordered list of free partitions of size `partition_sz` each, where the value of `partition_sz` is the one originally supplied to the `segregate` call. Respectively, the condition `if (next != static_cast<char *>(iter) + partition_size)` inside the `try_malloc_n()` method is checking whether the next free partition immediately follows the current free partition or not. This condition only works correctly if `partition_size` has exactly the same value as the `partition_sz` supplied earlier to `segregate`, otherwise `static_cast<char *>(iter) + partition_size` doesn't produce the end boundary of the current partition. Thus, with any other value of `partition_size` the outcome of the condition is pretty much random.  
  
Thus, prior to #53, the `while (--n != 0)` loop in `try_malloc_n` was simply looking for additional free partitions immediately following the current free partition, all partitions adding up to a contiguous chunk of `n` partitions in total. With the added in #53 `if (n == 1)` condition, the function now fails for single-partition blocks whenever it's considering a single-partition-sized free hole, even for a correct value of `partition_size`. With other values of `partition_size` the outcomes are more random (e.g. the function might inadvertently incorporate an already allocated partition into the found "free" chunk).  
  
Besides the above, the condition `if (n == 1)` also looks highly suspicious per se, as it's not clear why chunks of size 1 need any extra handling compared to chunks of larger sizes.  
  
  
**The present PR consists (at the time of the opening) of 3 commits:**  
  
2822004a This commit adds two unit tests to `test_pool_alloc.cpp` demonstrating the regression caused by `simple_segregated_storage` as broken functionality in `boost::pool`. Similar tests could have been implemented for `simple_segregated_storage` instead, but it was decided to do those in `test_pool_alloc.cpp`, as the latter tests the public API of the library, while `simple_segregated_storage` is more of an internal detail class. Both tests sequentially allocate 3 chunks of sizes equal to 1 partition each, expecting the chunks to be allocated immediately following each other in memory.  
  
In the first test the pool block has the size of exactly 3 partitions, respectively the 3rd chunk, due to the regression from #53 is no longer allocated within the first pool block and gets allocated in an additionally allocated pool block, leading to the 3rd chunk being further away from the 2nd one than expected (so the test in line 310 fails). Upon freeing and reallocating the 2nd chunk the reallocated chunk may randomly appear in a different position than originally, the respective test condition `ptr_1a == ptr_1` in line 317 randomly failing or not failing. The randomness is due to undefined memory positions of the two pool blocks relative to each other. If the second pool block gets allocated at a smaller address than the first one, the reallocated 2nd chunk will be picked up from the 2nd pool block, thus appearing at a different address than before (where it was picked up from the 1st pool block), so the test in line 317 fails. If the 2nd pool block gets allocated at a larger address than the 1st one, then the partition originally occupied by the 2nd chunk is the first partition in the list of free partitions and thus will be reallocated (since the 3rd partition in the block is still free due to the bug we're discussing, the same bug doesn't kick in during reallocation of the 2nd chunk), respectively the test in line 317 does not fail. This can be confirming by adding a debug print statement into the test code, printing the addresses of the 3 chunks.  
  
In the second test the pool block has the size of 4 partitions, thus the bug introduced by #53 doesn't kick in during the initial 3 allocations of the chunks. However, as the 3rd chunk is now at the expected position (immediately following the 2nd chunk), the bug does kick in during the reallocation of the 2nd chunk, leading to failing test in line 342.  
  
492f9a89 This commit reverts the `if (n == 1)` condition introduced by #53 and causing the regression. This causes some of the unit tests introduced by #53 (those which test against the varying `partition_sz` value) to fail.  
  
86b79870 This commit removes the two failing tests (of the 4 tests introduced into `test_simple_seg_storage.cpp` by #53). These tests explicitly test against the case of the varying `partition_sz` value, which is explicitly unsupported per the above discussion. Thus they are not supposed to succeed at all.  
  
**Further remarks**  
  
The [Example 4.1](https://theboostcpplibraries.com/boost.pool#ex.pool_01) linked from the [Issue #52](https://github.com/boostorg/pool/issues/52), looks completely wrong, as it is supplying an inconsistent value of partition size. This example only works by random chance, due to the fact that only a single partition (of a wrong size, but size doesn't matter in this case) is requested. This example was apparently the reason for the confusion in [Issue #52](https://github.com/boostorg/pool/issues/52), which [PR #53](https://github.com/boostorg/pool/pull/53) tried to fix. But by the previous argument, it cannot be fixed, the `simple_segregated_storage` cannot handle partitions of mixed sizes.

---

## Comment 1

> Username: fdetro  
> Created_at: 2025-01-24 10:56:07 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2612241163  

@jzmaddock This PR is blocking a release for us, could you please comment?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2025-01-24 12:25:08 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2612408754  

@leimao ??

---

## Comment 3

> Username: leimao  
> Created_at: 2025-01-24 17:03:04 UTC  
> Updated_at: 2025-01-24 17:04:12 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2612997234  

I remember my PR was to reject requesting partitions of mixed sizes from `simple_segregated_storage` (it will return nullptr if requested so IIRC). If you revert my PR, the behavior of Example 4.1 will become undefined again.  
  
You are free to change my implementation to fix the bugs you are encountering, but the existing tests should not be deleted.

---

## Review 4 [Commented]

> Username: leimao  
> Created_at: 2025-01-24 17:06:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pool/pull/63#pullrequestreview-2573178660  

📁 test/test_simple_seg_storage.cpp

```diff
 341 |- 
 342 |-             void* pvret = tstore.malloc_n(2, 2 * partition_sz);
 343 |-             BOOST_TEST(pvret == 0);
```

> Username: leimao  
> Created_at: 2025-01-24 17:06:24 UTC  
> Updated_at: 2025-01-24 17:06:34 UTC  
> Url: https://github.com/boostorg/pool/pull/63#discussion_r1928995829  

We want this to return a `nullptr`. The existing tests shall not be deleted.

> Username: fdetro  
> Created_at: 2025-01-28 10:19:14 UTC  
> Url: https://github.com/boostorg/pool/pull/63#discussion_r1931899929  

@leimao IMHO this call should fail with an assertion, not by returning a `nullptr`.  
  
The documentation of the class states:  
  
> Simple Segregated Storage is the simplest, and probably the fastest,  
memory allocation/deallocation algorithm.  It is responsible for  
partitioning a memory block into **fixed-size chunks**: where the block comes from   
is determined by the client of the class.  
  
The API design of `simple_segregated_storage` somewhat implies that one might add blocks with different partition sizes and `malloc_n` blocks with different partition sizes, but the implementation does not cover any of these cases (and so the available tests - expect the ones added with the debated PR).  
  
Factually,  `simple_segregated_storage` is an implementation detail of `pool` and the class should be in the `detail` namespace. Discussing this class in a _boost_ book is also a bad idea, especially when providing examples that do not work correctly.   
  
I consider the added tests as wrong, so IMHO there is no need that these succeed.

> Username: fdetro  
> Created_at: 2025-02-18 07:21:51 UTC  
> Updated_at: 2025-02-18 07:22:09 UTC  
> Url: https://github.com/boostorg/pool/pull/63#discussion_r1959192322  

@jzmaddock @leimao How can we make progress on this topic?


---

## Comment 5

> Username: jzmaddock  
> Created_at: 2025-02-18 11:44:22 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2665431805  

I think the essential problem we have here is that this is an old unmaintained library...  But in order:  
  
* I think it's too late to move simple_segregated_storage to be a detail, that ship has sailed.  
* I agree that the docs make it clear that simple_segregated_storage works with blocks of fixed size only, it also states that preconditions are unchecked, and that demons may be lurking for the unwary: https://www.boost.org/doc/libs/1_87_0/libs/pool/doc/html/boost_pool/pool/pooling.html#boost_pool.pool.pooling.simple_segregated.  
* The referenced examples are from a third party book: they're not part of Boost or written by us, so I'm not too bothered if they're broken.  
* On that basis, it looks like I was wrong to accept @leimao patch, so my apologies there.  
  
@leimao was there a specific use case for returning NULL when the library is used erroneously?  
  
@fdetro what are we still missing in terms of tests?

---

## Comment 6

> Username: fdetro  
> Created_at: 2025-02-18 13:19:27 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2665697322  

> @fdetro what are we still missing in terms of tests?  
  
From our point of view tests are OK now. We've added one for the reallocation regression in `boost::pool` and removed the ones testing invalid chunk size allocations in `simple_segregated_storage` (added by @leimao in the other PR).

---

## Comment 7

> Username: fdetro  
> Created_at: 2025-02-25 10:01:06 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2681407534  

@jzmaddock Can we finalize / merge this? We would love to have this in the next boost release.

---

## Comment 8

> Username: jeking3  
> Created_at: 2025-02-25 12:46:24 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2681872088  

This should have run all the tests in GitHub Actions.  Please check the annotations error on the action workflow and resolve that, and refresh the PR.  We didn't run all the tests so this should not be merged.  
  
https://github.com/boostorg/pool/actions/runs/12789993276

---

## Comment 9

> Username: fdetro  
> Created_at: 2025-02-25 14:00:40 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2682075310  

> This should have run all the tests in GitHub Actions. Please check the annotations error on the action workflow and resolve that, and refresh the PR. We didn't run all the tests so this should not be merged.  
>   
> https://github.com/boostorg/pool/actions/runs/12789993276  
  
This states:  
  
> Please verify your email address to run GitHub Actions workflows. https://github.com/settings/emails  
  
My email address is verified, but I do not see any option to re-run the workflows. Are you able to do this?

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2025-02-25 16:40:06 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2682611447  

Hmm, I don't see any reason why they wouldn't run, unless it's related to https://github.com/actions/runner-images/issues/11101 ?

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2025-02-25 19:01:31 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2682999404  

CI tests are running here: https://github.com/boostorg/pool/pull/65

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2025-02-25 19:23:00 UTC  
> Url: https://github.com/boostorg/pool/pull/63#issuecomment-2683045722  

Merged to develop.

---
