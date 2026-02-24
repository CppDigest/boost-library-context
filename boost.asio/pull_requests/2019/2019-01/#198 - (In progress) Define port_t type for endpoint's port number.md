# #198 (In progress) Define port_t type for endpoint's port number [Closed]

> Username: tiendq  
> Created at: 2019-01-17 04:44:01 UTC  
> Updated at: 2019-01-17 04:48:42 UTC  
> Closed at: 2019-01-17 04:46:16 UTC  
> Url: https://github.com/boostorg/asio/pull/198  

Just inspired by `size_t`, would it be a little more readable to use `port_t` instead of `unsigned short`? If yes I'll check and update everywhere.  
  
And it can be used in consumer code too, for example:  
  
```  
class ServerBase {  
public:  
  ServerBase(boost::asio::port_t port = 8000) {}  
};  
```

---
