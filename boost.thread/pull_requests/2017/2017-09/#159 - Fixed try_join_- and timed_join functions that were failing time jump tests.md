# #159 Fixed try_join_* and timed_join functions that were failing time jump tests [Merged]

> Username: austin-beer  
> Created at: 2017-09-21 17:35:16 UTC  
> Updated at: 2017-09-21 22:48:17 UTC  
> Merged at: 2017-09-21 22:37:21 UTC  
> Closed at: 2017-09-21 22:37:21 UTC  
> Url: https://github.com/boostorg/thread/pull/159  

Here are the test results with this PR. try_join_*() and timed_join() now work correctly on both Linux and Windows.  
  
[linux_results_austin_no_monotonic.txt](https://github.com/boostorg/thread/files/1322217/linux_results_austin_no_monotonic.txt)  
[linux_results_austin_yes_monotonic.txt](https://github.com/boostorg/thread/files/1322218/linux_results_austin_yes_monotonic.txt)  
[windows_results_austin_no_monotonic.txt](https://github.com/boostorg/thread/files/1322216/windows_results_austin_no_monotonic.txt)

---

## Comment 1

> Username: austin-beer  
> Created_at: 2017-09-21 18:11:16 UTC  
> Url: https://github.com/boostorg/thread/pull/159#issuecomment-331237693  

Fixed the try_lock_*for() and wait_for() functions as well. Here are the updated test results with those commits.  
  
[linux_results_austin_no_monotonic.txt](https://github.com/boostorg/thread/files/1322304/linux_results_austin_no_monotonic.txt)  
[linux_results_austin_yes_monotonic.txt](https://github.com/boostorg/thread/files/1322305/linux_results_austin_yes_monotonic.txt)  
[windows_results_austin_no_monotonic.txt](https://github.com/boostorg/thread/files/1322306/windows_results_austin_no_monotonic.txt)  
  
On Linux it looks like the only remaining tests that are failing and are fixable are the DateTime functions.

---

## Comment 2

> Username: viboes  
> Created_at: 2017-09-21 19:32:31 UTC  
> Url: https://github.com/boostorg/thread/pull/159#issuecomment-331258330  

hi, I'm waiting for the windows results before merging :(

---

## Comment 3

> Username: viboes  
> Created_at: 2017-09-21 19:32:58 UTC  
> Url: https://github.com/boostorg/thread/pull/159#issuecomment-331258445  

BTW, thank you very much.

---
