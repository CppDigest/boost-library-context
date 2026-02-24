# #998 - Best practice for running a repeating background task [Closed]

> Username: ghost  
> Created at: 2018-01-27 09:47:44 UTC  
> Updated at: 2018-04-12 21:41:06 UTC  
> Closed at: 2018-04-12 21:41:06 UTC  
> Url: https://github.com/boostorg/beast/issues/998  

Hi,   
While a beast based server process is running, what would be the best practice to run a non-blocking task using beats, which is triggered every minute, for instance, a URL spider?   
  
Many thanks,

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-27 14:40:48 UTC  
> Url: https://github.com/boostorg/beast/issues/998#issuecomment-360989040  

Use `basic_deadline_timer` to invoke your completion handler once per minute, then implement your spider as an asynchronous HTTP client. The example program "http-crawl" demonstrates this use case.

---

## Comment 2

> Username: ghost  
> Created at: 2018-01-28 06:17:13 UTC  
> Url: https://github.com/boostorg/beast/issues/998#issuecomment-361041252  

Thank you so much for your prompt reply. I will try that out.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-27 16:40:29 UTC  
> Url: https://github.com/boostorg/beast/issues/998#issuecomment-368942186  

Has this issue been resolved?

---

## Comment 4

> Username: sehe  
> Created at: 2018-03-25 13:36:55 UTC  
> Url: https://github.com/boostorg/beast/issues/998#issuecomment-375971122  

Consider also http://www.boost.org/doc/libs/1_66_0/doc/html/thread/synchronization.html#thread.synchronization.executors.rationale.scheduled_work  
  
It looks like this should be usable in conjunction with Asio's executors (though a small adaptor could be required)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-04-12 21:41:06 UTC  
> Url: https://github.com/boostorg/beast/issues/998#issuecomment-380952975  

It looks like this has been resolved and there's no recent activity so I'll go ahead and close it, feel free to open new issues as needed!
