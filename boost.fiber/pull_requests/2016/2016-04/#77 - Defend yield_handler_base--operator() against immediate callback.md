# #77 Defend yield_handler_base::operator() against immediate callback. [Merged]

> Username: nat-goodspeed  
> Created at: 2016-04-02 16:17:31 UTC  
> Updated at: 2016-06-06 11:36:34 UTC  
> Merged at: 2016-04-02 17:17:32 UTC  
> Closed at: 2016-04-02 17:17:32 UTC  
> Url: https://github.com/boostorg/fiber/pull/77  

If the async operation invoked by the asio async function immediately calls  
yield_handler_base::operator() even before control reaches  
async_result_base::get(), which would suspend the calling fiber, the context*  
bound by yield_handler_base's constructor is still the active() context. This  
may not be passed to context::migrate(). It probably shouldn't be passed to  
context::set_ready(), either.

---
