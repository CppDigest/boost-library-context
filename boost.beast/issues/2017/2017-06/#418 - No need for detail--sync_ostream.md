# #418 - No need for detail::sync_ostream [Closed]

> Username: vinniefalco  
> Created at: 2017-06-04 00:13:07 UTC  
> Updated at: 2017-06-05 23:07:08 UTC  
> Closed at: 2017-06-05 23:07:08 UTC  
> Url: https://github.com/boostorg/beast/issues/418  

`operator<<` can be rewritten to use a straight `serializer`.
