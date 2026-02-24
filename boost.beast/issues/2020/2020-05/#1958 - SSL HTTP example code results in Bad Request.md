# #1958 - SSL HTTP example code results in Bad Request [Closed]

> Username: scm-ns  
> Created at: 2020-05-20 01:22:24 UTC  
> Updated at: 2023-09-22 15:10:36 UTC  
> Closed at: 2020-05-20 01:55:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1958  

### Version of Beast  
1.71  
  
### Steps necessary to reproduce the problem  
```  
    void make_req() {  
        auto const host = "https://www.github.com";  
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
  
I am using clang. BoringSSL instead of OpenSSL.   
I am using bazel and  nelhage_rules_boost adding boost asio/beast as a dependency.   
  
I have been trying to debug this, and nothing seems to work. I keep getting   
```  
GET / HTTP/1.0  
Host: https://www.github.com  
User-Agent: Boost.Beast/266  
Accept: */*  
  
  
HTTP/1.1 400 Bad Request  
Connection: close  
Content-Length: 0  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-20 01:25:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-631178262  

Why HTTP/1.0? Use 1.1 and see what happens.

---

## Comment 2

> Username: scm-ns  
> Created at: 2020-05-20 01:26:13 UTC  
> Updated at: 2020-05-20 01:26:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-631178529  

I was using 1.1, changed it to 1.0 to just test.   
  
Thank for the fast response.

---

## Comment 3

> Username: scm-ns  
> Created at: 2020-05-20 01:26:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-631178759  

```  
GET / HTTP/1.1  
Host: https://www.github.com  
User-Agent: Boost.Beast/266  
Accept: */*  
  
  
HTTP/1.1 400 Bad Request  
Connection: close  
Content-Length: 0  
```

---

## Comment 4

> Username: scm-ns  
> Created at: 2020-05-20 01:28:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-631179249  

I am using bazel and  nelhage_rules_boost adding boost asio/beast as a dependency.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-05-20 01:34:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-631180836  

Try this instead:  
```  
auto const results = resolver.resolve( "www.github.com", "https" );  
```

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-05-20 01:48:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-631184767  

Use  
```  
auto const results = resolver.resolve( "newworlds-server.herokuapp.com", "https" );  
```

---

## Comment 7

> Username: scm-ns  
> Created at: 2020-05-20 01:51:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-631185630  

Yep figured it out. Thank you.   
In the host which is set as the header I was putting : https://new-worlds*  instead of new-worlds.   
I spend many hours on this silly mistake.   
  
Thank you.   
  
Curious why does your resolver code work and not the one that I had?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-05-20 01:54:39 UTC  
> Updated at: 2020-05-20 01:54:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-631186405  

"https://www.github.com" is a URL, not a valid host. The host portion, "www.github.com", is what you need to pass to the resolver.

---

## Comment 9

> Username: scm-ns  
> Created at: 2020-05-20 02:15:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-631192068  

Thanks. dumb mistake on my end.

---

## Comment 10

> Username: auric  
> Created at: 2023-09-22 15:10:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1958#issuecomment-1731586856  

example above throws exception while trying to perform `handshake`
