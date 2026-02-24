# #82 docs: thread_interruption never calls terminate() [Merged]

> Username: akrzemi1  
> Created at: 2016-04-20 07:10:34 UTC  
> Updated at: 2016-04-20 21:19:05 UTC  
> Merged at: 2016-04-20 21:19:05 UTC  
> Closed at: 2016-04-20 21:19:05 UTC  
> Url: https://github.com/boostorg/thread/pull/82  

This note in the docs tries to clarify that exception `thread_interrupted` leaving the thread-main function never triggers a call to `std::terminate`. The previous wording used word 'terminate' in a way that made the semantics ambiguous.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-04-20 21:19:01 UTC  
> Url: https://github.com/boostorg/thread/pull/82#issuecomment-212609143  

Thanks.

---
