# #120 - adaptive_storage looses no-overflow guarantee when weighted fills are used [Closed]

> Username: HDembinski  
> Created at: 2018-10-10 13:14:15 UTC  
> Updated at: 2019-01-31 19:53:11 UTC  
> Closed at: 2019-01-31 19:53:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/120  

Steven wrote:  
> It seems that using weighted fills breaks the special  
overflow handling properties of adaptive_storage. I  
think that this deserves a distinct note.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-10-10 13:15:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/120#issuecomment-428566891  

This may be solved by not allowing the automatic switch to weighted fills.

---

## Comment 2

> Username: HDembinski  
> Created at: 2018-12-21 07:47:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/120#issuecomment-449294915  

The adaptive_storage does not support weighted fills any more, but when the internal counters are converted to double, the no-overflow guarantee is still lost. I think this should be fixed by returning a different type after multiplying, subtracting or dividing, which is not a histogram any more, so you cannot fill it. Then, the adaptive_storage does not even have to support double counters, because the return type can have a storage of double.
