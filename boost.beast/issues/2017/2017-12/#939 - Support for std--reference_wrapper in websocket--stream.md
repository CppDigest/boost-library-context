# #939 - Support for std::reference_wrapper in websocket::stream [Closed]

> Username: djarek  
> Created at: 2017-12-15 06:39:25 UTC  
> Updated at: 2017-12-31 19:20:50 UTC  
> Closed at: 2017-12-31 19:20:50 UTC  
> Url: https://github.com/boostorg/beast/issues/939  

Should `websocket::stream<std::reference_wrapper<Stream>>` be allowed?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-12-15 12:28:15 UTC  
> Url: https://github.com/boostorg/beast/issues/939#issuecomment-351994096  

Nope. Should `asio::ssl::stream<std::reference_wrapper<asio::ip::tcp::socket>>` be alllowed?  
  
This, however, works:  
```  
websocket::stream<asio::ip::tcp::socket&> ws{ioc};  
```

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-12-31 19:20:50 UTC  
> Url: https://github.com/boostorg/beast/issues/939#issuecomment-354619983  

I think this is resolved; feel free to re-open if not, thanks.
