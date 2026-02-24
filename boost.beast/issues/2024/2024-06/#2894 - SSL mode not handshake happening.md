# #2894 - SSL mode not handshake happening [Closed]

> Username: EkbalCode  
> Created at: 2024-06-28 20:17:47 UTC  
> Updated at: 2024-07-24 06:20:22 UTC  
> Closed at: 2024-07-24 06:20:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2894  

I am using example/http/client/async-ssl/http_client_async_ssl.cpp   
  
// The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::tlsv12_client};  
  
    // This holds the root certificate used for verification  
    load_root_certificates(ctx);  
  
    // Verify the remote server's certificate  
    ctx.set_verify_mode(ssl::verify_peer);  
  
This code execution not making handshake so in turn I am getting 404 status code .  
  
Kindly help

---

## Comment 1

> Username: ashtum  
> Created at: 2024-07-01 06:08:07 UTC  
> Updated at: 2024-07-01 06:09:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2894#issuecomment-2199313680  

> This code execution not making handshake.  
  
The handshake happens in the following line. What is the error message on failure?  
  
```  
        // Perform the SSL handshake  
        stream_.async_handshake(  
            ssl::stream_base::client,  
            beast::bind_front_handler(  
                &session::on_handshake,  
                shared_from_this()));  
```  
  
> so in turn I am getting 404 status code .  
  
Do you mean an HTTP 404 error? This shouldn't be related to the handshake process because if the connection fails to perform the handshake correctly, it can't make any request at all.
