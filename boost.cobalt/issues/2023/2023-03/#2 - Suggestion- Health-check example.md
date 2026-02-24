# #2 - Suggestion: Health-check example [Open]

> Username: mzimbres  
> Created at: 2023-03-01 07:37:08 UTC  
> Updated at: 2023-03-19 12:57:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/2  

Async could provide a health check as an example since it is simple enough to implement and useful in general. Constraints  
  
* Ping the server periodically (can be a http endpoint).  
* Use only one timer.  
* The timer should not be cancelled.  
* On expiration the timer should exit with success.  
  
The interface could look like  
  
```cpp  
co_await health_check(connection, std::chrono::seconds{1});  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-03-19 12:57:33 UTC  
> Url: https://github.com/boostorg/cobalt/issues/2#issuecomment-1475246824  

I am not sure I get what this supposed to do.
