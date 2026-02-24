# #9 Coroutine [Closed]

> Username: martong  
> Created at: 2014-04-08 13:37:33 UTC  
> Updated at: 2014-06-13 11:19:41 UTC  
> Closed at: 2014-04-08 15:01:45 UTC  
> Url: https://github.com/boostorg/coroutine/pull/9  

Hi Oliver. It would be great if it would be possible to identify which coroutine is executed at a given thread. This pull request is about to have a get_id() function, which returns the actual running coroutine's id. Similiar as the case with boost::this_thread::get_id().   
  
Why would that be useful? With this approach it is not needed to pass through the coroutine's stack it's identifier. No matter how deep we are in the stack, get_id will tell us which coroutine is running. By identifying coroutines, coroutine specific global storage could be created, in a higher abstraction layer. One high level aim could be to have specific log prefixes for each and every coroutine, without polluting all the functions with an extra additional parameter which holds that string. Debugging coroutines is a little bit tricky today (you cannot print all the backtraces for all coroutines at a time), therefore this kind of logging would be very important.  
  
Please let me know your thoughts about this idea, whether is it completely insane or not. :)  
Many thanks,  
Gábor

---

## Comment 1

> Username: olk  
> Created_at: 2014-04-08 15:01:45 UTC  
> Url: https://github.com/boostorg/coroutine/pull/9#issuecomment-39858606  

fibers (boost.fiber) are intended to return an identifier (boost::this_fiber::get_id() etc.)

---
