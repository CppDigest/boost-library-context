# #52 - Feature request: enable_local_shared_from_this [Closed]

> Username: FooBarWidget  
> Created at: 2018-09-12 07:21:02 UTC  
> Updated at: 2018-09-12 13:25:00 UTC  
> Closed at: 2018-09-12 13:25:00 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/52  

I would like to use `local_shared_ptr` because I need the performance and don't need the synchronization guarantees. But this library is currently lacking local version of `enable_shared_from_this`. I would like to request such a feature addition.

---

## Comment 1

> Username: pdimov  
> Created at: 2018-09-12 11:47:38 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/52#issuecomment-420619038  

There is no need for a separate local base class; use `enable_shared_from_this` and assign the result of `shared_from_this` to `local_shared_ptr`.  
  
As currently implemented, it's not possible to make the hypothetical `local_shared_from_this` more efficient than the ordinary `shared_from_this`, even if all such calls are guaranteed to originate from the same thread.

---

## Comment 2

> Username: FooBarWidget  
> Created at: 2018-09-12 13:25:00 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/52#issuecomment-420646716  

Thanks for the response. I ended up finding a solution in the form of `intrusive_ptr`.
