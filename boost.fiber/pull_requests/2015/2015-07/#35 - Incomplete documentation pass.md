# #35 Incomplete documentation pass [Merged]

> Username: nat-goodspeed  
> Created at: 2015-07-20 03:36:26 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2015-07-20 17:40:03 UTC  
> Closed at: 2015-07-20 17:40:03 UTC  
> Url: https://github.com/boostorg/fiber/pull/35  

I'm submitting this pull request even though I have not yet fleshed out customization.qbk. (I've removed the redundancy between scheduling.qbk and customization.qbk. As per our email exchange, scheduling.qbk now contains reference material for the relevant classes, while customization.qbk will be more of a cookbook description of the steps you must take to use them.)  
  
I've actually changed the sched_algorithm_with_properties<> API slightly: instead of introducing an awakened_props() method, it now uses an awakened(fiber_context\* f, PROPS& props) overload. That makes more sense to me than my original idea.  
  
I did change the actual context-switching call from boost::context::operator()() to boost::context::resume() so that it would build against Boost 1.58. I hope that's not a problem.  
  
I added documentation placeholders for future (and shared_future) get_exception_ptr(), which weren't yet mentioned. Please correct these? I think both get() and get_exception_ptr() should say something about the wait behavior. But I don't know what to say about get() when the promise is set with an exception, or get_exception_ptr() when the promise is set with a value.  
  
I also added documentation placeholders for mutex (etc., etc., etc.) lock(), try_lock(), unlock(), try_lock_for(), try_lock_until() methods. Please fill these in?  
  
I added a note about BOOST_FIBERS_THREADSAFE, although I don't know whether you have decided to continue supporting that macro and its absence. When you decide, please do let me know: the documentation is not yet consistent; in at least one other place it promises that Fiber synchronization primitives always work across threads.  
  
I think I understand the purpose of sched_algorithm::ready_fibers(), but I'm perplexed by its signature and the round_robin implementation. The definition returns std::size_t rather than bool, and round_robin::ready_fibers() appears to return the inverse of what the name implies: it returns queue.empty(), which will convert to 0 when there are in fact ready fibers and nonzero when there are none. I would have expected that if you just want to know whether there are or are not ready fibers, you'd return bool, and round_robin would return (! queue.empty()). Returning std::size_t appears to mean that you specifically want to know _how many_ ready fibers there are, which implies counting them. Even in that case, though, I would have assumed that 0 means "no fibers ready" rather than the reverse.

---
