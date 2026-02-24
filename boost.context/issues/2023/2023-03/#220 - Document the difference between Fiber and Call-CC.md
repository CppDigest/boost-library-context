# #220 - Document the difference between Fiber and Call/CC [Closed]

> Username: naman-dixit-srib  
> Created at: 2023-03-06 11:46:52 UTC  
> Updated at: 2023-08-18 05:04:07 UTC  
> Closed at: 2023-08-18 05:04:06 UTC  
> Url: https://github.com/boostorg/context/issues/220  

The [documentation](https://www.boost.org/doc/libs/1_81_0/libs/context/doc/html/index.html) and example code for both is near identical, and there is no explicitly documented difference between the two (or between them and `execution_context` and `coroutine`).  
  
Could this be rectified, as it is very hard to those unfamiliar with this particular library to figure what subtle difference is there between all these alternatives?

---

## Comment 1

> Username: olk  
> Created at: 2023-08-18 05:04:06 UTC  
> Url: https://github.com/boostorg/context/issues/220#issuecomment-1683357583  

call/cc is based on an historic API  
fiber_context is the API proposed in "P0876: fiber_context - fibers without scheduler" (C++ standardisation).
