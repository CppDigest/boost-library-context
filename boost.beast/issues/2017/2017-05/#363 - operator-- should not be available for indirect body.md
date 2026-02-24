# #363 - operator<< should not be available for indirect body [Closed]

> Username: vinniefalco  
> Created at: 2017-05-14 21:46:18 UTC  
> Updated at: 2017-06-03 22:07:34 UTC  
> Closed at: 2017-06-03 22:07:34 UTC  
> Url: https://github.com/boostorg/beast/issues/363  

`operator<<` should only be available when the entire body fits in memory as a single buffer sequence?
