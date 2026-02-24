# #30 - ExecutionContext v2 Guidance [Closed]

> Username: habemus-papadum  
> Created at: 2016-04-15 12:34:22 UTC  
> Updated at: 2016-04-15 16:15:14 UTC  
> Closed at: 2016-04-15 16:15:14 UTC  
> Url: https://github.com/boostorg/context/issues/30  

Hi,   
I was hoping to get a some guidance about ExecutionContext v2.  Are the following statements correct?  
- Execution context v2 is currently a WIP (not part of any Boost release)  
- Execution context v2 is planned for Boost 1.61 (if so, is there a timeframe?)  
- Execution context v2 is essentially similar in spirt to the symmetric_coro that will/may be removed from Boost.coroutine  
  
Are there any mailing list threads that discuss the semantics of the new execution context, rationale, comparisons with the old.    
  
Thanks!

---

## Comment 1

> Username: olk  
> Created at: 2016-04-15 16:15:14 UTC  
> Url: https://github.com/boostorg/context/issues/30#issuecomment-210525959  

- ecv2 is a new API  
- ecv2 will be the standard ec of boost.context in boost-1.61  
- ecv2 provides symmetric context switching  
- symmetric_coroutine will remain in boost.coroutine  
- boost.coroutine will be marked as deprecated soon  
- boost.coroutine2 is based on ecv2 and will replace boost.coroutine
