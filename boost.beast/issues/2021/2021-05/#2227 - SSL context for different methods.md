# #2227 - SSL context for different methods [Closed]

> Username: Tectu  
> Created at: 2021-05-10 12:01:23 UTC  
> Updated at: 2021-05-10 15:15:15 UTC  
> Closed at: 2021-05-10 15:15:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2227  

I'm trying to write a small webserver based on beast. Currently I am finalizing the implementation for handling plain or TLS connections.  
  
The examples usually do something along the lines of:  
```cpp  
boost::asio::ssl::context ctx{ boost::asio::ssl::context::tlsv12 };  
```  
  
From modern webservers such as nginx, we're used being able to specify a list of supported encryption methods. For example, TLSv1.2 **and** TLSv1.3 to allow clients to connect using either TLSv1.2 **or** TLSv1.3.  
  
How would one implement this using beast? The constructor of `boost::asio::context` only accepts one method.  
Is the trick to create the context with whatever method is requires/sane? If so, how would one go about detecting which one is suitable - ie which ones the client supports?

---

## Comment 1

> Username: imerr  
> Created at: 2021-05-10 12:05:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2227#issuecomment-836614964  

I believe the intended way to do this is to disable versions you don't want/need after creation  
For example:  
```c++  
boost::asio::ssl::context sslContext(boost::asio::ssl::context::tls_server)  
sslContext.set_options(boost::asio::ssl::context::no_sslv2);  
sslContext.set_options(boost::asio::ssl::context::no_sslv3);  
sslContext.set_options(boost::asio::ssl::context::no_tlsv1_1);  
```

---

## Comment 2

> Username: ddevienne  
> Created at: 2021-05-10 12:19:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2227#issuecomment-836628631  

Having a recommended set of SSL/TLS options for secure servers, which disable old and/or broken options, would be nice. Something that starts out perhaps too secure by default even, and one can then slowly relax as needed. Having to figure those out on one's own leads to too many not-secure-enough server otherwise, I'm afraid. Mine included.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-10 12:25:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2227#issuecomment-836634107  

I wrote some code for this that was based on best practice 2 years ago.   
  
Give me 20 mins

---

## Comment 4

> Username: Tectu  
> Created at: 2021-05-10 14:11:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2227#issuecomment-836750202  

> Give me 20 mins  
  
Show us, show us, show us!!! 😄

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-05-10 14:17:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2227#issuecomment-836757105  

```  
    void configure_server_context(asio::ssl::context &ssl_context, options const &opts)  
    {  
        SSL_CTX_set_cipher_list(ssl_context.native_handle(),  
                                "ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-"  
                                "SHA256:ECDHE-RSA-AES128-GCM-"  
                                "SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-"  
                                "SHA256:DHE-RSA-AES256-GCM-"  
                                "SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-"  
                                "RSA-AES256-SHA384:ECDHE-"  
                                "RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:"  
                                "DHE-RSA-AES128-SHA256:"  
                                "DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:"  
                                "ECDHE-RSA-DES-CBC3-SHA:"  
                                "EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:"  
                                "AES128-SHA:AES256-SHA:"  
                                "DES-CBC3-SHA:!DSS");  
  
        ssl_context.set_options(asio::ssl::context::default_workarounds | asio::ssl::context::no_tlsv1 |  
                                asio::ssl::context::single_dh_use | SSL_OP_CIPHER_SERVER_PREFERENCE);  
  
        auto cert = ""s < posix::open(opts.server_cert_file());  
        auto key  = ""s < posix::open(opts.server_key_file());  
  
        ssl_context.use_certificate_chain(asio::buffer(cert));  
        ssl_context.use_private_key(asio::buffer(key), asio::ssl::context::pem);  
    }  
```

---

## Comment 6

> Username: madmongo1  
> Created at: 2021-05-10 14:47:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2227#issuecomment-836791250  

I initialised the context with argument: `asio::ssl::context::method::tls_server`

---

## Comment 7

> Username: Tectu  
> Created at: 2021-05-10 15:03:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2227#issuecomment-836810074  

This seems very helpful - thank you for sharing?  
May I borrow this to integrate this into my beast based webserver library (currently MIT licensed)?

---

## Comment 8

> Username: madmongo1  
> Created at: 2021-05-10 15:10:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2227#issuecomment-836818985  

Sure

---

## Comment 9

> Username: Tectu  
> Created at: 2021-05-10 15:15:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2227#issuecomment-836824414  

Thank you Sir, much appreciated!
