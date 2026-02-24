# #1773 - Explore async_compose in the implementation of composed operations [Closed]

> Username: vinniefalco  
> Created at: 2019-11-27 01:32:51 UTC  
> Updated at: 2022-09-24 05:51:32 UTC  
> Closed at: 2022-09-24 05:51:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1773  

It might be useful to rewrite some of the composed operations in terms of `async_compose`. We should do one of them to compare the differences. If anything, it will help take advantage of upstream asio changes such as the nested executor_type on the initiation object.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-05 14:18:29 UTC  
> Updated at: 2020-06-05 14:18:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1773#issuecomment-639525348  

Observations so far:  
  
- Simple composed operations where there is no more than one outstanding async operation at any one time are more easily expressed using async_compose because:  
  - no requirement for async_initiate boilerplate which adds zero value to the program  
  - associated executor, allocator and work guards are handled automatically  
  
- Composed operations that need to allocate memory from the associated allocator would benefit  
  from a subset of stable_async_base to aid with automatic memory deallocation  
  
- Composed operations that manage more than one simultaneous async operation are less suitable  
  as they require the composed_op to fork, which involves writing a new type in any case.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:51:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1773#issuecomment-1256873689  

Gentlemen, I usually would ask if this is still pending, but since `async_compose` is used the code-base by now, I thinkg this is resolved..
