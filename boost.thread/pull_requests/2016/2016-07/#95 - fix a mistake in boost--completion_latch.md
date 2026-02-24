# #95 fix a mistake in boost::completion_latch [Merged]

> Username: rick68  
> Created at: 2016-07-25 23:39:27 UTC  
> Updated at: 2016-08-07 11:00:31 UTC  
> Merged at: 2016-08-07 11:00:31 UTC  
> Closed at: 2016-08-07 11:00:31 UTC  
> Url: https://github.com/boostorg/thread/pull/95  

The constructor `completion_latch(std::size_t count, void(*funct)())` does not use any template arguments.

---
