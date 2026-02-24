# #3040 - stream.shutdown hangs [Closed]

> Username: cdschreiber  
> Created at: 2025-10-08 15:21:37 UTC  
> Updated at: 2025-10-08 15:24:24 UTC  
> Closed at: 2025-10-08 15:24:24 UTC  
> Url: https://github.com/boostorg/beast/issues/3040  

I'm trying the http_client_sync_ssl example and stream.shutdown(ec) at line 111 hangs.   
  
macOS 15.6.1 (Sequoia)  
clang++ 21  
boost 1.89  
openssl 3.6
