# #2752 - is it possible to implement body_.file_.async_read(buf_, amount, ec) ? [Closed]

> Username: zhllxt  
> Created at: 2023-10-20 01:56:15 UTC  
> Updated at: 2023-10-23 13:42:18 UTC  
> Closed at: 2023-10-23 13:42:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2752  

When using single threaded coroutines, i think the synchronous read function of file_body will block the current thread, which reduces efficiency.  
  
so, is it possible to implement body_.file_.async_read(buf_, amount, ec) ? or some implementation can achieve this goal.  
  
```cpp  
auto const nread = body_.file_.read(buf_, amount, ec);  
```

---

## Comment 1

> Username: fpelliccioni  
> Created at: 2023-10-20 08:36:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2752#issuecomment-1772315134  

Thank you for pointing out this.  
  
Your observation seems to make sense. Before making any decisions, I will conduct some tests to verify your concern regarding synchronous reads blocking the thread in a single-threaded coroutine environment. If the tests confirm your observation, we will consider changing the implementation to be non-blocking.  
  
We appreciate you bringing this to our attention.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2023-10-20 14:17:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2752#issuecomment-1772825123  

No we are not supporting asynchronous file I/O. That's a mess due to the variation in operating system support. If you want that you need to write it yourself, and it will be platform specific. You might try Asio's asynchronous file I/O. But be aware that on some platforms it is simulated with internal threads making synchronous calls.
