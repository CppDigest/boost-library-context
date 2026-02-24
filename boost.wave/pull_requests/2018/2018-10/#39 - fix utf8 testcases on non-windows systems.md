# #39 fix utf8 testcases on non-windows systems [Merged]

> Username: rlenhardt  
> Created at: 2018-10-06 11:01:57 UTC  
> Updated at: 2018-10-06 13:30:30 UTC  
> Merged at: 2018-10-06 13:30:30 UTC  
> Closed at: 2018-10-06 13:30:30 UTC  
> Url: https://github.com/boostorg/wave/pull/39  

The failures t_9_025 and t_9_026 were caused by me using backslashes in the expected results. By using forward slashes the result check should work on all systems.  The failures of t_9_027 and t_9_028 are system-specific -- we cannot expect these tests to fail on systems that already use UTF-8 for path encoding. To be honest, these tests were just a check for me that the new /U yes path imbue switch was just local to a single test and didn't mess up other tests and they can be used for demonstration purposes that you have to switch to UTF8 path conversions on Windows. t_9_025 and t_9_026 alone are able to show that you can now open includes with UTF-8 encoded paths on all systems. I think t_9_027 and t_9_028 can be left out of the full test suite so i disabled  them in test.cfg.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2018-10-06 13:30:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/39#pullrequestreview-162257007  

Thanks for your quick response!

---
