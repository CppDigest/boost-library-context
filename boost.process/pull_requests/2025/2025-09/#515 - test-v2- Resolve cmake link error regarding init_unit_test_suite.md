# #515 test/v2: Resolve cmake link error regarding init_unit_test_suite [Closed]

> Username: nigels-com  
> Created at: 2025-09-04 03:25:00 UTC  
> Updated at: 2025-10-06 04:49:18 UTC  
> Closed at: 2025-10-06 04:49:18 UTC  
> Url: https://github.com/boostorg/process/pull/515  

Fix for [boostorg/boost Issue #1033](https://github.com/boostorg/boost/issues/1033)  
  
I'd come across a problem building boost 1.89.0 on Ubuntu 24.04 (Noble) in `-DBUILD_TESTING=Y` mode.  
Turned out the same had been reported previously as mentioned above.  
  
This change resolves the problem for me, seems like the same as being done for `test/v1` tests.  
  
Working build for [boostorg/boost](https://github.com/boostorg/boost)  
  
```  
cmake .. --fresh -G Ninja -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=Y && ninja  
```

---

## Comment 1

> Username: nigels-com  
> Created_at: 2025-09-04 03:26:16 UTC  
> Url: https://github.com/boostorg/process/pull/515#issuecomment-3251660125  

MS copilot says:  
  
The pull request "test/v2: Resolve cmake link error regarding init_unit_test_suite" addresses build issues experienced when building Boost 1.89.0 on Ubuntu 24.04 with testing enabled, specifically related to the `init_unit_test_suite` link error noted in boostorg/boost Issue #1033. The PR updates seven test files in the `test/v2` directory to add `#define BOOST_TEST_MAIN` at the top of each file. This aligns the v2 tests with the solution already used in the v1 tests.  
  
- No files were deleted and only straightforward additions were made (no deletions).  
- The change is minimal and targeted, reducing risk.  
- The PR is marked as mergeable, but with an "unstable" mergeable state (which may be due to CI or branch status).  
- There are no code comments, requested reviewers, or review comments so far.  
  
Overall, the fix is clear, resolves a previously reported issue, and is consistent with existing project practices for similar test files. The change should help users building with CMake and testing enabled.

---

## Comment 2

> Username: james-sergeant-work  
> Created_at: 2025-09-15 07:30:02 UTC  
> Url: https://github.com/boostorg/process/pull/515#issuecomment-3290833147  

@nigels-com Fancy seeing you here :) I was hoping the fix was as simple as this. Thanks - hope you're doing well.

---
