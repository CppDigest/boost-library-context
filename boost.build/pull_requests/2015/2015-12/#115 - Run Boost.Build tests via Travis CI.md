# #115 Run Boost.Build tests via Travis CI. [Merged]

> Username: tee3  
> Created at: 2015-12-14 11:04:59 UTC  
> Updated at: 2021-10-02 22:20:08 UTC  
> Merged at: 2016-02-04 08:19:06 UTC  
> Closed at: 2016-02-04 08:19:06 UTC  
> Url: https://github.com/boostorg/build/pull/115  

This pull request enables running the Boost.Build automated tests (with Python) via Travis CI.  Note that this is currently failing and this pull request does not address the cause of the failures.  These should probably be resolved prior to merging this pull request.  Unfortunately, I don't have the expertise to actually fix the issues with any confidence.  
  
This pull request runs the automated tests for Boost.Build by running `python test_all.py` against each toolset.  It does not run the tests enabled via the `--extras` flag since the requirements for these tests are not met by the Travis CI infrastructure.  
  
When building `b2` with `gcc`, there are two errors with both Python 2.6 and Python 2.7  
- [ ] path_features  
- [ ] library_order  
  
When building `b2` with `clang`, there is on error with both Python 2.6 and Python 2.7  
- [ ] path_features  
  
See https://github.com/boostorg/build/pull/110 for the original description of this branch.

---

## Comment 1

> Username: vprus  
> Created_at: 2016-02-04 08:22:24 UTC  
> Url: https://github.com/boostorg/build/pull/115#issuecomment-179705612  

I've merged this, thanks! We still have the open issue with library_test.py on modern gcc, I'll see whether the issue happens in travis and possibly will disable the test if so.

---

## Comment 2

> Username: tee3  
> Created_at: 2016-02-04 12:49:57 UTC  
> Url: https://github.com/boostorg/build/pull/115#issuecomment-179815489  

Very nice, and thank you!

---
