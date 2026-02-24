# #916 - Tidy up ssl_socket [Closed]

> Username: vinniefalco  
> Created at: 2017-12-01 00:18:03 UTC  
> Updated at: 2017-12-09 22:25:58 UTC  
> Closed at: 2017-12-09 22:25:58 UTC  
> Url: https://github.com/boostorg/beast/issues/916  

Updated Asio has better constructors for `ssl::stream` so the hack in `ssl_socket` can be removed.
