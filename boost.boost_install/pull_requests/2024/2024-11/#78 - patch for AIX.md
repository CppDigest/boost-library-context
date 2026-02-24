# #78 patch for AIX [Open]

> Username: tjcw  
> Created at: 2024-11-05 14:08:31 UTC  
> Updated at: 2024-11-12 15:45:32 UTC  
> Url: https://github.com/boostorg/boost_install/pull/78  

This PR adds support for openxl and xlclang toolchains on AIX

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-11-05 14:55:20 UTC  
> Url: https://github.com/boostorg/boost_install/pull/78#issuecomment-2457391904  

I assume this is dependent on https://github.com/boostorg/build/pull/778, which should presumably be re-issued against the upstream https://github.com/bfgroup/b2?

---

## Comment 2

> Username: tjcw  
> Created_at: 2024-11-05 15:54:42 UTC  
> Url: https://github.com/boostorg/boost_install/pull/78#issuecomment-2457557528  

I raised the PR on the repository where the affected files were. Should I raise an issue in the b2 repository pointing to this PR ?

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-11-05 16:05:06 UTC  
> Url: https://github.com/boostorg/boost_install/pull/78#issuecomment-2457582802  

If you're asking whether you need to open a pull request in https://github.com/bfgroup/b2, the answer is yes, that's the main repository for b2 nowadays. boostorg/build is updated regularly from bfgroup/b2.

---

## Comment 4

> Username: tjcw  
> Created_at: 2024-11-05 16:17:42 UTC  
> Url: https://github.com/boostorg/boost_install/pull/78#issuecomment-2457612909  

OK, raised in that repo as https://github.com/bfgroup/b2/pull/421

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-11-05 16:25:09 UTC  
> Url: https://github.com/boostorg/boost_install/pull/78#issuecomment-2457630801  

No, that's not what I meant. The changes to b2 in https://github.com/boostorg/build/pull/778 need to be raised against bfgroup/b2, not the Boost-specific bootstrap changes.

---

## Comment 6

> Username: tjcw  
> Created_at: 2024-11-06 09:45:49 UTC  
> Url: https://github.com/boostorg/boost_install/pull/78#issuecomment-2459149504  

OK, raised as https://github.com/bfgroup/b2/pull/422

---

## Comment 7

> Username: tjcw  
> Created_at: 2024-11-12 15:31:47 UTC  
> Url: https://github.com/boostorg/boost_install/pull/78#issuecomment-2470849090  

The tests that failed were not due to the code change in this PR. One test timed out at 60 minutes, presumably because MSVC was slow. The other 2 failures were environment-related; the test needed a zip/unzip command which was unable to be installed.  
  
Please retest as appropriate.

---

## Comment 8

> Username: pdimov  
> Created_at: 2024-11-12 15:45:30 UTC  
> Url: https://github.com/boostorg/boost_install/pull/78#issuecomment-2470883947  

Yes, I knew that these failures weren't caused by the change in this PR. They were caused by problems in Log and Iostreams, which have been now fixed.  
  
But I don't think there's any need to merge this PR before the changes to b2, because without them, the b2 toolset can never be `xlclang` or `openxl`.

---
