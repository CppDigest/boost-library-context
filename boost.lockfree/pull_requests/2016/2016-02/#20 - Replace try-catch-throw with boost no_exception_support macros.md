# #20 Replace try/catch/throw with boost no_exception_support macros [Merged]

> Username: cdglove  
> Created at: 2016-02-19 03:46:09 UTC  
> Updated at: 2016-06-11 09:25:01 UTC  
> Merged at: 2016-06-11 09:25:01 UTC  
> Closed at: 2016-06-11 09:25:01 UTC  
> Url: https://github.com/boostorg/lockfree/pull/20  

I hit this today working on a platform without exception support. I believe this is safe because the exception would have to have been emitted from user code which, if exceptions are disabled, wouldn't happen.

---
