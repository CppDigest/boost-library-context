# #304 - value_to overloads for array, object, string [Closed]

> Username: vinniefalco  
> Created at: 2020-09-08 23:33:38 UTC  
> Updated at: 2020-09-16 16:51:18 UTC  
> Closed at: 2020-09-16 16:51:18 UTC  
> Url: https://github.com/boostorg/json/issues/304  

Otherwise, `value_to` on those types will construct a `value`, making an unnecessary copy.
