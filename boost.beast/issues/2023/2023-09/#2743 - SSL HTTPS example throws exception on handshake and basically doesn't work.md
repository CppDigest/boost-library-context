# #2743 - SSL HTTPS example throws exception on handshake and basically doesn't work [Closed]

> Username: auric  
> Created at: 2023-09-22 15:16:48 UTC  
> Updated at: 2023-12-31 10:56:10 UTC  
> Closed at: 2023-12-31 10:56:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2743  

I've also tried async example, it has the same issue: handshake cannot be performed  
  
### Version of Beast  
  
1.83  
  
### Steps necessary to reproduce the problem  
```  
    void make_req() {  
        auto const host = "www.github.com";  
        auto const port = "443";  
        auto const target = "/";  
        int version = 11;  
  
        // The io_context is required for all I/O  
        net::io_context ioc;  
  
        // The SSL context is required, and holds certificates  
        ssl::context ctx(ssl::context::tlsv12_client);  
        ctx.set_default_verify_paths();  
        // Verify the remote server's certificate  
        ctx.set_verify_mode(ssl::verify_peer);  
  
            // These objects perform our I/O  
        tcp::resolver resolver(ioc);  
        beast::ssl_stream<beast::tcp_stream> stream(ioc, ctx);  
  
        // Set SNI Hostname (many hosts need this to handshake successfully)  
        if(! SSL_set_tlsext_host_name(stream.native_handle(), host))  
        {  
            beast::error_code ec;  
            throw beast::system_error{ec};  
        }  
  
        // Look up the domain name  
        auto const results = resolver.resolve(host, port);  
  
        // Make the connection on the IP address we get from a lookup  
        beast::get_lowest_layer(stream).connect(results);  
  
        // Perform the SSL handshake  
        stream.handshake(ssl::stream_base::client);  
  
        // Set up an HTTP GET request message  
        http::request<http::string_body> req{http::verb::get, target, version};  
        req.set(http::field::host, host);  
        req.set(http::field::user_agent, BOOST_BEAST_VERSION_STRING);  
        req.set(http::field::accept, "*/*");  
  
        std::cout << req << std::endl;  
  
        // Send the HTTP request to the remote host  
        http::write(stream, req);  
  
        // This buffer is used for reading and must be persisted  
        beast::flat_buffer buffer;  
  
        // Declare a container to hold the response  
        http::response<http::dynamic_body> res;  
  
        // Receive the HTTP response  
        http::read(stream, buffer, res);  
  
        // Write the message to standard out  
        std::cout << res << std::endl;  
  
        // Gracefully close the stream  
        beast::error_code ec;  
        stream.shutdown(ec);  
        if(ec == net::error::eof)  
        {  
            // Rationale:  
            // http://stackoverflow.com/questions/25587403/boost-asio-ssl-async-shutdown-always-finishes-with-an-error  
            ec = {};  
        }  
        if(ec)  
            throw beast::system_error{ec};  
  
    }  
```  
  
### All relevant compiler information  
Microsoft Visual C++ Compiler 17.4.33205.214

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-09-22 16:29:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2743#issuecomment-1731710084  

Same issue s what? What are you connecting too?

---

## Comment 2

> Username: auric  
> Created at: 2023-09-23 08:17:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2743#issuecomment-1732251132  

> Same issue s what? What are you connecting too?  
  
I'm trying to connect `www.github.com` using `http_client_sync_ssl.cpp` example, but it always throws exception in   
```  
    // Perform the SSL handshake  
    stream.handshake(ssl::stream_base::client);  
```

---

## Comment 3

> Username: auric  
> Created at: 2023-09-23 08:32:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2743#issuecomment-1732254399  

![screenshot](https://github.com/boostorg/beast/assets/15901/17721aa1-d45a-4921-ad8a-91e8e2ecb5e6)  
  
the same problem happens even if the host is `google.com`

---

## Comment 4

> Username: ashtum  
> Created at: 2023-10-29 17:42:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2743#issuecomment-1784179528  

@auric, I can connect with no issues using your provided example code. However, it seems there might be an issue with finding the proper certificates in this line of code:  
  
```C++  
ctx.set_default_verify_paths();  
```  
  
You may find the answer provided in this link helpful: https://stackoverflow.com/a/40710806/13354736
