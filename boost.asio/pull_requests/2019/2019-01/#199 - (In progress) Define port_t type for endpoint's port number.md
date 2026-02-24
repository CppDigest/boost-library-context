# #199 (In progress) Define port_t type for endpoint's port number [Open]

> Username: tiendq  
> Created at: 2019-01-17 04:53:14 UTC  
> Updated at: 2019-01-21 12:00:43 UTC  
> Url: https://github.com/boostorg/asio/pull/199  

Just inspired by `size_t`, would it be a little more readable to use `port_t` instead of `unsigned short`? If yes I'll check and update everywhere.  
  
And it can be used in consumer code too, for example:  
```  
class ServerBase {  
public:  
  ServerBase(boost::asio::port_t port = 8000) {}  
};  
```

---

## Comment 1

> Username: viccpp  
> Created_at: 2019-01-21 12:00:43 UTC  
> Url: https://github.com/boostorg/asio/pull/199#issuecomment-456050564  

POSIX calls this type `in_port_t`.  
http://pubs.opengroup.org/onlinepubs/000095399/basedefs/netinet/in.h.html

---
