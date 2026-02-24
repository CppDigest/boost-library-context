# #3 - [feature] Immediate promise [Closed]

> Username: klemens-morgenstern  
> Created at: 2023-03-19 12:58:59 UTC  
> Updated at: 2024-05-02 10:30:45 UTC  
> Closed at: 2024-05-02 10:30:45 UTC  
> Url: https://github.com/boostorg/cobalt/issues/3  

It might be possible to create a promise that's immediate & never allocates, e.g.:  
  
```cpp  
promise<int>::now(42);  
```  
  
This does however seem like it should be handled by the optimizer, some time in the future, so I don't know if it's worth bothering with that to avoid a single pooled allocation.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-07-14 17:24:44 UTC  
> Url: https://github.com/boostorg/cobalt/issues/3#issuecomment-1636161551  

If done, this could be used with use_op and immediate_execution.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2023-07-14 17:43:58 UTC  
> Url: https://github.com/boostorg/cobalt/issues/3#issuecomment-1636182404  

For completeness sake: the main purpose is to optimize out allocations in situations where we still want to use a `promise` type for API reasons, and not just a generic awaitable. It's possible but should be driven by use-cases.
