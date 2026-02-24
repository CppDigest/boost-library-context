# #2839 - support SSLKEYLOGFILE env var for wireshark [Closed]

> Username: Kyle-Thompson  
> Created at: 2024-03-19 22:20:15 UTC  
> Updated at: 2024-03-20 17:41:40 UTC  
> Closed at: 2024-03-20 17:41:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2839  

I'm currently trying to migrate a system that communicates with AWS via CURL to one using boost beast. I've managed to get all the headers to line up but the requests using boost beast are still failing. To debug this I've tried examining the network traffic with tcpdump+wireshark but this is challenging since the data is encrypted. When examining the happy case with curl I'm able to get the relevant keys by `export SSLKEYLOGFILE=~/keys.log` before running the command but no such option exists with boost beast.  
  
How can I get the TLSv1.3 keys necessary to supply them to wireshark so I can properly compare and contrast my boost beast implementation with the curl one?  
  
Thanks!

---

## Comment 1

> Username: Kyle-Thompson  
> Created at: 2024-03-19 23:21:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2839#issuecomment-2008331848  

I managed to resolve my particular issue but this feature request still remains.

---

## Comment 2

> Username: ashtum  
> Created at: 2024-03-20 07:13:49 UTC  
> Url: https://github.com/boostorg/beast/issues/2839#issuecomment-2008927207  

[ssl::context::native_handle](https://www.boost.org/doc/libs/1_84_0/doc/html/boost_asio/reference/ssl__context/native_handle.html) provides access to the underlying `SSL_CTX`. Using [SSL_CTX_set_keylog_callback](https://www.openssl.org/docs/man1.1.1/man3/SSL_CTX_set_keylog_callback.html), you can define the TLS key logging callback to log the generated keys. For instance:  
  
```C++  
ssl::context ctx{ ssl::context::tlsv12_client };  
  
SSL_CTX_set_keylog_callback(  
  ctx.native_handle(),  
  +[](const SSL* ssl, const char* line)  
  {  
    std::ofstream key_log{ "key_log.log", std::ios_base::app };  
    key_log << line << std::endl;  
  });  
```  
Note the `+` operator preceding the lambda converts the stateless lambda into a function pointer.

---

## Comment 3

> Username: Kyle-Thompson  
> Created at: 2024-03-20 17:41:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2839#issuecomment-2010204207  

That's excellent, thank you!
