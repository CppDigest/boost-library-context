# #497 Remove dependencies on Boost.StaticAssert [Merged]

> Username: Lastique  
> Created at: 2026-01-22 17:25:12 UTC  
> Updated at: 2026-02-06 13:01:34 UTC  
> Merged at: 2026-01-22 23:24:13 UTC  
> Closed at: 2026-01-22 23:24:13 UTC  
> Url: https://github.com/boostorg/mysql/pull/497  

Boost.StaticAssert has been merged into Boost.Config, so remove the dependency.

---

## Comment 1

> Username: anarthal  
> Created_at: 2026-01-22 19:32:05 UTC  
> Url: https://github.com/boostorg/mysql/pull/497#issuecomment-3786310176  

Looks like there's a bunch of CI failures due to this. For instance: https://drone.cpp.al/boostorg/mysql/1134/2/3  
  
Is this because libraries like `boost_align` haven't merged PRs like this one yet? (I.e. can I safely ignore this error and expect it to go away in a couple of days?)

---

## Comment 2

> Username: Lastique  
> Created_at: 2026-01-22 20:53:49 UTC  
> Url: https://github.com/boostorg/mysql/pull/497#issuecomment-3786642360  

Yes. `test/cmake_test/CMakeLists.txt` manually adds dependent libraries as subdirectories without actually tracking dependencies though the superproject. And since some of the dependent libraries have not yet merged the PRs and still reference `Boost::static_assert`, CMake can't find that target and errors out. This should resolve once those PRs are merged. You may want to wait until the dependent PRs are merged, just to be safe.

---

## Comment 3

> Username: anarthal  
> Created_at: 2026-01-22 23:16:58 UTC  
> Url: https://github.com/boostorg/mysql/pull/497#issuecomment-3787242931  

Since we know the cause, I will merge this for now. Could I please ask that you notified us on the mailing list once all PRs have been merged so I can double-check? Thanks.

---

## Comment 4

> Username: Lastique  
> Created_at: 2026-01-23 00:17:31 UTC  
> Url: https://github.com/boostorg/mysql/pull/497#issuecomment-3787518985  

Ok. In case if you want to monitor progress, the PRs are tracked in [this](https://github.com/boostorg/static_assert/issues/19) issue.

---

## Comment 5

> Username: Lastique  
> Created_at: 2026-02-06 13:01:34 UTC  
> Url: https://github.com/boostorg/mysql/pull/497#issuecomment-3860327696  

> Could I please ask that you notified us on the mailing list once all PRs have been merged so I can double-check? Thanks.  
  
I think, the libraries mentioned in `test/cmake_test` are merged now, you may try running the CI to check that.

---
