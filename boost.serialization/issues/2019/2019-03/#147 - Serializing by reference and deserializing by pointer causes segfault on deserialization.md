# #147 - Serializing by reference and deserializing by pointer causes segfault on deserialization [Closed]

> Username: usernameak  
> Created at: 2019-03-07 21:45:02 UTC  
> Updated at: 2019-03-08 02:25:12 UTC  
> Closed at: 2019-03-08 02:25:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/147  



---

## Comment 1

> Username: robertramey  
> Created at: 2019-03-07 21:51:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/147#issuecomment-470709087  

correct.  Do not to that.  the easiest way to avoid the problem is to prefer using the same serialization function for both input and output using the & operator
