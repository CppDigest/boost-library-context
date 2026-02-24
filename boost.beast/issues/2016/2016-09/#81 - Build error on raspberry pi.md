# #81 - Build error on raspberry pi [Closed]

> Username: amitv87  
> Created at: 2016-09-20 07:28:54 UTC  
> Updated at: 2016-09-20 07:34:05 UTC  
> Closed at: 2016-09-20 07:34:05 UTC  
> Url: https://github.com/boostorg/beast/issues/81  

I get missing  **boost/endian/buffers.hpp**, not sure if there is boost module for this  
I installed boost with this command *_sudo apt-get install libboost-all-dev *_  
  
```  
In file included from Beast/include/beast/websocket/detail/stream_base.hpp:14:0,  
                Beast/include/beast/websocket/option.hpp:12,  
                Beast/include/beast/websocket.hpp:12,  
                Beast/examples/websocket_example.cpp:9:  
Beast/include/beast/websocket/detail/frame.hpp:18:36: fatal error: boost/endian/buffers.hpp: No such file or directory  
 #include <boost/endian/buffers.hpp>  
```

---

## Comment 1

> Username: amitv87  
> Created at: 2016-09-20 07:34:05 UTC  
> Url: https://github.com/boostorg/beast/issues/81#issuecomment-248226090  

Oops only works with 1.58+
