# #435 - Build failing with Apple Clang 16 [Open]

> Username: a-soll  
> Created at: 2024-09-17 21:06:23 UTC  
> Updated at: 2024-09-19 01:47:57 UTC  
> Url: https://github.com/boostorg/asio/issues/435  

```  
/opt/homebrew/include/boost/asio/basic_socket_acceptor.hpp:473:45: error: no matching constructor for initialization of 'protocol_type' (aka 'boost::asio::ip::tcp')  
  void open(const protocol_type& protocol = protocol_type())  
                                            ^  
<unknown>:0: note: in instantiation of default function argument expression for 'open<boost::asio::ip::tcp, boost::asio::any_io_executor>' required here  
/opt/homebrew/include/boost/asio/ip/tcp.hpp:143:12: note: candidate constructor not viable: requires single argument 'protocol_family', but no arguments were provided  
  explicit tcp(int protocol_family) noexcept  
           ^  
/opt/homebrew/include/boost/asio/ip/tcp.hpp:46:7: note: candidate constructor (the implicit copy constructor) not viable: requires 1 argument, but 0 were provided  
class tcp  
      ^  
/opt/homebrew/include/boost/asio/ip/tcp.hpp:46:7: note: candidate constructor (the implicit move constructor) not viable: requires 1 argument, but 0 were provided  
class tcp  
```  
  
Updated clang today and I'm getting this issue when building. Is there a workaround or anything in the meantime?

---

## Comment 1

> Username: a-soll  
> Created at: 2024-09-19 01:47:11 UTC  
> Updated at: 2024-09-19 01:47:57 UTC  
> Url: https://github.com/boostorg/asio/issues/435#issuecomment-2359803655  

I added a default constructor and the issue went away, but I'm not sure if that's a good idea or not. Haven't been able to test the build.
