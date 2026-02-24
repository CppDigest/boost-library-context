# #87 Fixed https://github.com/boostorg/uuid/issues/86 [Closed]

> Username: BinCaoWR  
> Created at: 2018-11-13 08:17:39 UTC  
> Updated at: 2019-06-25 17:21:16 UTC  
> Closed at: 2019-06-25 17:21:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/87  



---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2018-11-13 09:32:34 UTC  
> Updated_at: 2019-06-24 02:09:00 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#issuecomment-438198084  

# [Codecov](https://codecov.io/gh/boostorg/uuid/pull/87?src=pr&el=h1) Report  
> Merging [#87](https://codecov.io/gh/boostorg/uuid/pull/87?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/uuid/commit/14a90261c790a5ed84fdc97bf21a122f033ad9eb?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/uuid/pull/87/graphs/tree.svg?width=650&token=6falIr5npV&height=150&src=pr)](https://codecov.io/gh/boostorg/uuid/pull/87?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #87   +/-   ##  
========================================  
  Coverage    79.78%   79.78%             
========================================  
  Files           13       13             
  Lines          638      638             
  Branches       154      154             
========================================  
  Hits           509      509             
  Misses          17       17             
  Partials       112      112  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/uuid/pull/87?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/uuid/detail/md5.hpp](https://codecov.io/gh/boostorg/uuid/pull/87/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC91dWlkL2RldGFpbC9tZDUuaHBw) | `88.19% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/uuid/pull/87?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/uuid/pull/87?src=pr&el=footer). Last update [14a9026...7c90ba2](https://codecov.io/gh/boostorg/uuid/pull/87?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: jeking3  
> Created_at: 2018-11-13 13:44:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/uuid/pull/87#pullrequestreview-174367873  

📁 include/boost/uuid/detail/md5.hpp

```diff
 297 |+ /* 
 298 |+  * The md5 results are read 4 bytes at a time in hash_to_uuid() from
 299 |+  * basic_name_generator.hpp, so the endian must be considered here
```

> Username: jeking3  
> Created_at: 2018-11-13 13:44:49 UTC  
> Updated_at: 2019-06-11 08:13:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#discussion_r233041877  

The RFC specifies that:  
'''  
   Set octets zero through 3 of the time_low field to octets zero  
      through 3 of the hash.  
'''  
  
I think the issue could instead be on the "to string" output and not on the generation.

> Username: jeking3  
> Created_at: 2019-06-08 10:40:39 UTC  
> Updated_at: 2019-06-11 08:13:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#discussion_r291802612  

String generation is not the issue, so I am resolving this comment.


---

## Review 3 [Changes requested]

> Username: jeking3  
> Created_at: 2019-02-10 15:14:25 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/uuid/pull/87#pullrequestreview-201907708  

The changes to the test are invalid.  The test results should not do any endian byte swapping before the memcmp.

📁 test/test_md5.cpp

```diff
  50 |         boost::uuids::detail::md5::digest_type result;
  51 |         hash.get_digest(result);
  52 |+         for (boost::uint32_t j = 0; j < 4; ++j) {
```

> Username: jeking3  
> Created_at: 2019-02-10 15:13:52 UTC  
> Updated_at: 2019-06-11 08:13:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#discussion_r255341654  

The byte-swapping is incorrect here (for any endian platform).   
There should be a direct memory comparison (memcmp, below) matching the expectations above.

> Username: jeking3  
> Created_at: 2019-06-08 10:42:26 UTC  
> Updated_at: 2019-06-11 08:13:39 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#discussion_r291802656  

I still believe this comment is correct.  The result of get_digest should be the same in all cases, not conditional based on the platform.  The result is a series of raw bytes and therefore endian-unaware at this level.


---

## Review 4 [Changes requested]

> Username: jeking3  
> Created_at: 2019-06-08 10:44:15 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/uuid/pull/87#pullrequestreview-247363525  

I believe the unit test should not be endian-aware since it is comparing against a raw set of bytes and that raw set of bytes is always valid.  
  
Given some of the issues this PR brings up I am going to look into adding a big-endian build job to the Travis CI suite.  
  
I believe the endian change in copying the result out of the MD5 internals is a correct change to make, but the unit test change doesn't make sense.

---

## Review 5 [Changes requested]

> Username: jeking3  
> Created_at: 2019-06-11 16:00:08 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/uuid/pull/87#pullrequestreview-248278447  

I finally got a big-endian container running and test_md5 passes, so the issue is not in test_md5.  It is probably in the from/to string code.

---

## Comment 6

> Username: jeking3  
> Created_at: 2019-06-11 16:00:53 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#issuecomment-500908846  

Given that test_md5 passes on a big endian system I am going to close this PR.  These changes are unnecessary.

---

## Comment 7

> Username: jeking3  
> Created_at: 2019-06-11 16:32:49 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#issuecomment-500921863  

Actually, I think I was wrong in closing this prematurely.  I am going to leave it open until I finish my investigation on big endian.

---

## Comment 8

> Username: jeking3  
> Created_at: 2019-06-23 19:52:55 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#issuecomment-504781750  

Okay, I agree when copying out to a char buffer, endianness must be taken into account when copying from the result.  I was able to reproduce the issue in a big-endian multiarch container.  I have consumed this change into that work where I also aligned the sha1 and md5 tests to ensure they are testing at a byte level and not a 32-bit int level, just to be sure.  When I submit a PR for this I will mark it as closing this PR, but you can leave this one open.

---

## Comment 9

> Username: jeking3  
> Created_at: 2019-06-23 22:11:17 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#issuecomment-504791931  

I have the code fixed and working on little and big endian systems (producing the same results).  I'm currently working on a Travis CI multiarch docker container solution between boost-ci and jeking3/bdde with a redhat powerpc fedora release 25 image to ensure it stays fixed.

---

## Comment 10

> Username: BinCaoWR  
> Created_at: 2019-06-24 01:49:24 UTC  
> Url: https://github.com/boostorg/uuid/pull/87#issuecomment-504810767  

Great. Thanks for your fix. So this PR is no longer needed, we can close it.

---
