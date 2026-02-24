# #270 - prime table operation is not 32-bit safe [Closed]

> Username: vinniefalco  
> Created at: 2020-09-05 02:41:10 UTC  
> Updated at: 2020-09-06 22:09:03 UTC  
> Closed at: 2020-09-06 22:09:03 UTC  
> Url: https://github.com/boostorg/json/issues/270  

This line causes a warning:  
https://github.com/CPPAlliance/json/blob/adacd6670cd92ef5b8e2890550a7a6171e86d854/include/boost/json/impl/object.ipp#L512  
  
The way it is written, it looks like we can get a `*prime` that exceeds the max unsigned 32-bit integer. Then the assignment will underflow.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-09-05 21:35:14 UTC  
> Url: https://github.com/boostorg/json/issues/270#issuecomment-687666076  

I'll write the table so that the maximum value is `4294967295` on 32 bit
