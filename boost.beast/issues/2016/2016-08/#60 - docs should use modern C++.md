# #60 - docs should use modern C++ [Closed]

> Username: vinniefalco  
> Created at: 2016-08-25 00:08:43 UTC  
> Updated at: 2016-08-26 17:44:30 UTC  
> Closed at: 2016-08-26 17:44:29 UTC  
> Url: https://github.com/boostorg/beast/issues/60  

Documentation example code needs to be checked for modern C++. Specifically that constructed objects use braces instead of parenthesis.  
  
This snippet  
  
```  
boost::asio::ip::tcp::socket&& sock;  
...  
```  
  
should read  
  
```  
boost::asio::ip::tcp::socket sock;  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-08-26 17:44:29 UTC  
> Url: https://github.com/boostorg/beast/issues/60#issuecomment-242802691  

Fix is merged
