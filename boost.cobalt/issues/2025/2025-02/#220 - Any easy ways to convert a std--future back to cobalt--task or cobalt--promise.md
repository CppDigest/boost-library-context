# #220 - Any easy ways to convert a std::future back to cobalt::task or cobalt::promise? [Closed]

> Username: xiewei20082008  
> Created at: 2025-02-28 23:33:25 UTC  
> Updated at: 2025-03-01 14:54:40 UTC  
> Closed at: 2025-03-01 14:54:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/220  

Using the method can get a std::future type from a coroutine result.  
```  
 auto fut = cobalt::spawn(ctx, ..., asio::use_future)  
```  
  
Any advice on the other way around?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-03-01 14:54:39 UTC  
> Url: https://github.com/boostorg/cobalt/issues/220#issuecomment-2692269116  

No, `std::future` doesn't allow anything like this.
