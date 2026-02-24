# #1027 - Set reuse_address(true) in examples when binding [Closed]

> Username: vinniefalco  
> Created at: 2018-02-17 14:20:34 UTC  
> Updated at: 2018-02-22 00:54:29 UTC  
> Closed at: 2018-02-22 00:54:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1027  

Acceptors in the examples should set  
```  
set_option(boost::asio::socket_base::reuse_address(true))  
```  
to avoid "address already in use" errors on subsequent runs.
