# #161  Finished fixing the condition_variable[_any] timed_wait() functions. [Merged]

> Username: austin-beer  
> Created at: 2017-09-22 21:29:10 UTC  
> Updated at: 2017-09-23 06:16:50 UTC  
> Merged at: 2017-09-23 05:00:11 UTC  
> Closed at: 2017-09-23 05:00:11 UTC  
> Url: https://github.com/boostorg/thread/pull/161  

I know I said I wasn't going to submit any more PRs for awhile, but I just couldn't help myself. :)  
  
I updated the tests to include future and shared_future, per your request. I also added tests for the try_*lock*_*() functions that shared_mutex provides that the other mutex's don't. And since upgrade_mutex is just a typedef of shared_mutex, I removed the tests for upgrade_mutex.  
  
[test_boost_sleep.cpp.txt](https://github.com/boostorg/thread/files/1325852/test_boost_sleep.cpp.txt)  
[Makefile.linux.txt](https://github.com/boostorg/thread/files/1325853/Makefile.linux.txt)  
[Makefile.windows.txt](https://github.com/boostorg/thread/files/1325855/Makefile.windows.txt)  
  
Latest results from the develop branch (for comparison to see how far we've come):  
[linux_results_orig_no_monotonic.txt](https://github.com/boostorg/thread/files/1325859/linux_results_orig_no_monotonic.txt)  
[linux_results_orig_yes_monotonic.txt](https://github.com/boostorg/thread/files/1325857/linux_results_orig_yes_monotonic.txt)  
[windows_results_orig_no_monotonic.txt](https://github.com/boostorg/thread/files/1325858/windows_results_orig_no_monotonic.txt)  
  
Latest results from the timespec_clocks branch:  
[linux_results_fixed_no_monotonic.txt](https://github.com/boostorg/thread/files/1325899/linux_results_fixed_no_monotonic.txt)  
[linux_results_fixed_yes_monotonic.txt](https://github.com/boostorg/thread/files/1325900/linux_results_fixed_yes_monotonic.txt)  
[windows_results_fixed_no_monotonic.txt](https://github.com/boostorg/thread/files/1325863/windows_results_fixed_no_monotonic.txt)  
  
Results with these PR changes:  
[linux_results_austin_no_monotonic.txt](https://github.com/boostorg/thread/files/1325868/linux_results_austin_no_monotonic.txt)  
[linux_results_austin_yes_monotonic.txt](https://github.com/boostorg/thread/files/1325869/linux_results_austin_yes_monotonic.txt)  
[windows_results_austin_no_monotonic.txt](https://github.com/boostorg/thread/files/1325867/windows_results_austin_no_monotonic.txt)

---

## Comment 1

> Username: viboes  
> Created_at: 2017-09-23 04:59:56 UTC  
> Url: https://github.com/boostorg/thread/pull/161#issuecomment-331610927  

Thanks for this unexpected contribution ;-) and for adding more test.  
I'll try to fix the remaining DataTime related issues.

---
