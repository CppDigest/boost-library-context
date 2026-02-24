# #1014 - dynamic buffer input areas are not mutable [Closed]

> Username: vinniefalco  
> Created at: 2018-02-09 19:52:28 UTC  
> Updated at: 2018-02-22 00:54:29 UTC  
> Closed at: 2018-02-22 00:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1014  

We need to make the dynamic buffer input areas immutable and get rid of that hack for websocket stream. A user is erroneously using this mutability and causing problems for himself.
