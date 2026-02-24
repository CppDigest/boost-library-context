# #158 stackful coroutines. [Closed]

> Username: klemens-morgenstern  
> Created at: 2024-03-10 06:23:59 UTC  
> Updated at: 2024-11-09 00:13:55 UTC  
> Closed at: 2024-11-09 00:13:55 UTC  
> Url: https://github.com/boostorg/cobalt/pull/158  



---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2024-03-13 00:24:36 UTC  
> Url: https://github.com/boostorg/cobalt/pull/158#issuecomment-1992817367  

The only thing we're doing is misusing `std::coroutine_handle<void>::address` according to it's precondition.  
  
> Preconditions: addr was obtained via a prior call to address on an object whose type is a specialization of coroutine_handle[.](https://eel.is/c++draft/coroutine.handle.export.import#2.sentence-1)  
  
  
Additionally, it would be nice if we could change this restriction:  
  
> If a program declares an explicit or partial specialization of coroutine_handle, the behavior is undefined[.](https://eel.is/c++draft/coroutine.handle#general-2.sentence-1)

---
