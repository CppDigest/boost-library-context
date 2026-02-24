# #154 Cleanup [Merged]

> Username: austin-beer  
> Created at: 2017-09-19 22:22:11 UTC  
> Updated at: 2017-09-20 18:46:48 UTC  
> Merged at: 2017-09-20 18:43:36 UTC  
> Closed at: 2017-09-20 18:43:36 UTC  
> Url: https://github.com/boostorg/thread/pull/154  

* Deleted a couple of unnecessary calls to internal_clock_t::now() in v2/thread.hpp.  
* Deleted the hidden::sleep_until() functions, which are no longer being used.  
* Deleted the condition_variable::do_wait_for() function, which is no longer being used.  
* Deleted the sleep_mutex and sleep_condition variables in pthread/thread_data.hpp, which are no longer being used.  
  
I re-ran the tests and, as expected, there are no changes to the results from this PR.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-09-20 05:41:56 UTC  
> Url: https://github.com/boostorg/thread/pull/154#issuecomment-330750816  

Thanks again.

---
