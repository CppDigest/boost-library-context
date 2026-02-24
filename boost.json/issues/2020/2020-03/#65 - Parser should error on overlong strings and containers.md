# #65 - Parser should error on overlong strings and containers [Closed]

> Username: vinniefalco  
> Created at: 2020-03-28 17:21:19 UTC  
> Updated at: 2020-05-03 19:35:52 UTC  
> Closed at: 2020-05-03 19:35:52 UTC  
> Url: https://github.com/boostorg/json/issues/65  

The current code throws an exception when strings, arrays, or objects are too large. It should return an error instead.
