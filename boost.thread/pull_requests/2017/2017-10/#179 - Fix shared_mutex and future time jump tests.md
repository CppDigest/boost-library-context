# #179 Fix shared_mutex and future time jump tests [Merged]

> Username: austin-beer  
> Created at: 2017-10-13 17:54:52 UTC  
> Updated at: 2017-10-15 02:05:34 UTC  
> Merged at: 2017-10-14 17:10:37 UTC  
> Closed at: 2017-10-14 17:10:37 UTC  
> Url: https://github.com/boostorg/thread/pull/179  

This PR is composed of two commits.  
  
The first commit simply reverts `pthread/shared_mutex.hpp` and `future.hpp` back to the versions in `develop`, which reverts all of the changes we've made to these two files to date.  
  
The second commit re-fixes the time jump issues by using the predicate versions of the wait functions, which is a simpler solution than the one we were using before and which works even with the changes we made to avoid losing notifications.

---

## Comment 1

> Username: austin-beer  
> Created_at: 2017-10-13 18:02:51 UTC  
> Url: https://github.com/boostorg/thread/pull/179#issuecomment-336525845  

Here are the test results from this PR.  
  
https://github.com/austin-beer/test-time-jumps/blob/master/linux_results_austin_no_monotonic.txt  
https://github.com/austin-beer/test-time-jumps/blob/master/linux_results_austin_yes_monotonic.txt  
https://github.com/austin-beer/test-time-jumps/blob/master/windows_results_austin_no_generic_shared.txt  
https://github.com/austin-beer/test-time-jumps/blob/master/windows_results_austin_yes_generic_shared.txt  
  
The only fixable failures that still need to be fixed after this PR are in:  
```  
win32/basic_timed_mutex.hpp  
win32/basic_recursive_mutex.hpp  
win32/shared_mutex.hpp  
```

---

## Comment 2

> Username: viboes  
> Created_at: 2017-10-13 19:06:54 UTC  
> Url: https://github.com/boostorg/thread/pull/179#issuecomment-336541409  

Thanks a lot for almost fixing everything.  
There is some hope we could have this for boost-1.66.

---
