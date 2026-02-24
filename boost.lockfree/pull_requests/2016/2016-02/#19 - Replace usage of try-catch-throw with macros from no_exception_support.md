# #19 Replace usage of try/catch/throw with macros from no_exception_support [Closed]

> Username: cdglove  
> Created at: 2016-02-19 03:26:55 UTC  
> Updated at: 2016-02-19 03:41:15 UTC  
> Closed at: 2016-02-19 03:40:47 UTC  
> Url: https://github.com/boostorg/lockfree/pull/19  

I hit this today working on a platform without exception support. I believe this is safe because the exception would have to have been emitted from user code which, if exceptions are disabled, wouldn't happen.

---

## Comment 1

> Username: cdglove  
> Created_at: 2016-02-19 03:41:15 UTC  
> Url: https://github.com/boostorg/lockfree/pull/19#issuecomment-186033633  

I didn't want the second commit in here. I'll reissue a new pull request.

---
