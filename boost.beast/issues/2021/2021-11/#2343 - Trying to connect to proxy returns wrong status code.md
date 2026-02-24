# #2343 - Trying to connect to proxy returns wrong status code [Closed]

> Username: benstadin  
> Created at: 2021-11-16 03:13:47 UTC  
> Updated at: 2021-11-16 22:41:57 UTC  
> Closed at: 2021-11-16 22:41:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2343  

Using Boost version 1.76 I'm not able to get authenticated HTTPS async client connection working through a proxy. After some debugging, it looks like the status code is returned incorrectly in my on_read callback. Even though the buffer clearly contains a 401 the error code will be 200. I'm a bit guessing, but it seems like the http parser does not like the but also does not provide an alternative error code (like internal error). I've tested with a Squid Proxy and a Nginx proxy.   
  
The buffer contains the following data:  
  
`"HTTP/1.1 401 Unauthorized\r\nServer: nginx/1.11.9\r\nDate: Tue, 16 Nov 2021 02:58:30 GMT\r\nContent-Type: text/html\r\nContent-Length: 195\r\nConnection: keep-alive\r\nWWW-Authenticate: Basic realm=\"Restricted\"\r\n\r\n<html>\r\n<head><title>401 Authorization Required</title></head>\r\n<body bgcolor=\"white\">\r\n<center><h1>401 Authorization Required</h1></center>\r\n<hr><center>nginx/1.11.9</center>\r\n</body>\r\n</html>\r\n"`  
  
  
For testing agains Nginx reverse proxy I was simply using the following minimalistic setup (https://github.com/dtan4/nginx-basic-auth-proxy):  
  
```  
docker run \  
    --rm \  
    --name nginx-basic-auth-proxy \  
    -p 8080:80 \  
    -p 8090:8090 \  
    -e BASIC_AUTH_USERNAME=username \  
    -e BASIC_AUTH_PASSWORD=password \  
    -e PROXY_PASS=https://www.google.com \  
    -e SERVER_NAME=proxy.dtan4.net \  
    -e PORT=80 \  
    quay.io/dtan4/nginx-basic-auth-proxy  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-11-16 03:15:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969773122  

Gonna be honest, I have extreme skepticism that there is a bug where Beast returns the incorrect HTTP Status-Code !!! I will be interested to find out what the actual problem is.

---

## Comment 2

> Username: benstadin  
> Created at: 2021-11-16 03:30:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969803836  

Probably. Though whatever it is, it's quite hard to track down. Here are the main code snippets (quite a mess of code after some trial & error).   
  
In method on_proxy_read ec will be 200, even though the buffer contains a 401 (Nginx) or 407 (Squid) .   
  
  ```  
  // Connect to the proxy server  
    void connectToProxy(const std::string& host,  
                        const std::string& port,  
                        const std::string& target) {  
        proxyRequest_.method(http::verb::connect);  
        proxyRequest_.target(target);  
        if (port == "http" || port == "https") {  
            proxyRequest_.set(http::field::host, host);  
        } else {  
            proxyRequest_.set(http::field::host, host + ":" + port);  
        }  
          
        proxyRequest_.set(http::field::proxy_connection, "keep-alive");  
  
        // temporary  
        {  
            std::string authCred = "proxy_user:proxy_user";  
            std::string encodedAuthCred("");  
            const size_t len = authCred.length();  
            encodedAuthCred.resize(beast::detail::base64::encoded_size(len));  
            beast::detail::base64::encode(&encodedAuthCred[0], (const unsigned char*)authCred.c_str(), len);  
            const std::string proxyAuthHeader = "Basic " + encodedAuthCred;  
            // proxyRequest_.set(http::field::proxy_authorization, proxyAuthHeader);  
        }  
  
        resolver_.async_resolve(host, port, beast::bind_front_handler(&HTTPSSession::on_proxy_resolve, shared_from_this()));  
    }  
  
    void on_proxy_resolve(beast::error_code ec, tcp::resolver::results_type results) {  
        if (ec) {  
            std::cout << "Could not connect to proxy. Resolve failed with " << ec.message();  
            result_.setHttpCode(500);  
            result_.setErrorDescription("read: " + ec.message());  
            return;  
        }  
        boost::asio::async_connect(beast::get_lowest_layer(stream_).socket(),  
                                   results,  
                                   beast::bind_front_handler(&HTTPSSession::on_proxy_connect, shared_from_this()));  
    }  
  
    void on_proxy_connect(beast::error_code ec, tcp::resolver::results_type::endpoint_type) {  
        if (ec) {  
            std::cout << "Could not connect to proxy. Connect failed with: " << ec.message();  
            result_.setHttpCode(500);  
            result_.setErrorDescription("read: " + ec.message());  
            return;  
        }  
        int code = proxyRes_.result_int();  
        http::async_write(stream_.next_layer(),  
                          proxyRequest_,  
                          beast::bind_front_handler(&HTTPSSession::on_proxy_write, shared_from_this()));  
    }  
  
    void on_proxy_write(beast::error_code ec, std::size_t bytes_transferred) {  
        if (ec) {  
            std::cout << "Could not connect to proxy. Write failed with: " << ec.message();  
            result_.setHttpCode(500);  
            result_.setErrorDescription("proxy write: " + ec.message());  
            return;  
        }  
          
          
       // proxyParser_.skip(true);  
        http::async_read(stream_.next_layer(),  
                         proxyBuffer_,  
                         proxyRes_,  
                         beast::bind_front_handler(&HTTPSSession::on_proxy_read, shared_from_this()));  
    }  
  
    void on_proxy_read(beast::error_code ec, std::size_t bytes_transferred) {  
          
//  
        if (!proxyBuffer_.data().size()) {  
            return; //   
        }  
          
        http::parser<false, http::string_body> response_parser;  
       // error_code ec;  
        response_parser.eager(true);  
        auto parseres = response_parser.put(  
                    proxyBuffer_.data(), ec);  
        auto res2 = response_parser.release();  
        auto code = res2.result_int();  
        auto message = ec.message();  
          
//        http::parser<false, http::string_body> response_parser;  
//  
//        response_parser.put(boost::asio::buffer(data_buffer, bytes_transferred), http_error_code);  
////        http::response<http::string_body> responseWithoutABody(proxyBuffer_.data());  
//  
//        http::parser<false, http::string_body> proxyParser(responseWithoutABody);  
//        auto res = proxyParser.release();  
//        std::cout << "== Content length " << response_parser["Content-Length"] << " and body "  
//                  << response_parser.body().length() << std::endl;  
//        std::cout << "== Headers: " << response_parser.base() << std::endl;  
//                while (not (ec or response_parser.is_done() or emulated_stream.empty())) {  
//                    auto next     = std::min(packet_size(prng), emulated_stream.size());  
//                    auto consumed = response_parser.put(  
//                        boost::asio::buffer(emulated_stream.data(), next), ec);  
//  
//                    std::cout << "Consumed " << consumed << std::boolalpha  
//                              << "\tHeaders done:" << response_parser.is_header_done()  
//                              << "\tDone:" << response_parser.is_done()  
//                              << "\tChunked:" << response_parser.chunked()  
//                              << "\t" << ec.message() << std::endl;  
//  
//                    if (ec == http::error::need_more)  
//                        ec.clear();  
//  
//                    emulated_stream.remove_prefix(consumed);  
//                }  
  
          
//        boost::process::async_pipe pipe(stream_.next_layer().socket());  
//        for (http::response<http::string_body> res; !ec && read(pipe, proxyBuffer_, res, ec); res.clear()) {  
//            std::cout << "== Content length " << res["Content-Length"] << " and body "  
//                      << res.body().length() << std::endl;  
//            std::cout << "== Headers: " << res.base() << std::endl;  
//        }  
                                                              
        if (proxyRes_.result_int() == 407 || proxyRes_.result_int() == 401) {  
            // TODO: Authorization is required  
            std::cout << "Could not connect to proxy. Authorization required.";  
            result_.setHttpCode(500);  
            return;  
        } else if (proxyRes_.result_int() != 200) {  
            std::cout << "Could not connect to proxy. Read failed with: " << ec.message();  
            result_.setHttpCode(500);  
            result_.setErrorDescription("proxy read: " + ec.message());  
            return;  
        }  
          
        std::cout << proxyRes_ << std::endl;  
  
        // Perform the SSL handshake  
        stream_.async_handshake(ssl::stream_base::client, beast::bind_front_handler(&HTTPSSession::on_handshake, shared_from_this()));  
    }  
  
int lwc::HTTPSAsyncClient::handleRequest(const std::string& host,  
                                         const std::string& port,  
                                         http::verb method,  
                                         const std::string& target,  
                                         const std::string& contentType,  
                                         const std::map<std::string, std::string>& header,  
                                         const std::string& data,  
                                         const std::string& username,  
                                         const std::string& password,  
                                         std::string& result,  
                                         std::string& error,  
                                         int redirectCount,  
                                         std::function<bool(bool preverified, boost::asio::ssl::verify_context& ctx)> verifyCallback) {  
    if (!_valid) {  
        std::cout << "Error: HTTPS Client Request failed. Invalid or missing "  
                                       "certifiacte configuration.";  
        return 500;  
    }  
  
    // Check command line arguments.  
    // The io_context is required for all I/O  
    net::io_context ioc;  
  
    // The SSL context is required, and holds certificates  
    ssl::context ctx{ssl::context::tlsv12_client};  
  
    HTTPSAsyncResult requestResult;  
    int version = 11;  
      
    std::make_shared<HTTPSSession>(net::make_strand(ioc), ctx, _timeout, requestResult)  
            ->connectToProxy(host, port, target);  
      
    ioc.run();  
  
    if (requestResult.getHttpCode() >= 400) {  
        error = requestResult.getErrorDescription();  
    }  
  
    result = requestResult.getData();  
    return requestResult.getHttpCode();  
}  
```

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-11-16 03:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969812234  

What is the contents of `ec` here?  
```  
      auto parseres = response_parser.put( proxyBuffer_.data(), ec );  
```

---

## Comment 4

> Username: benstadin  
> Created at: 2021-11-16 03:38:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969818385  

When entering the callback:  
  
<img width="628" alt="image" src="https://user-images.githubusercontent.com/1433419/141891414-279558ea-b832-49b0-8f41-9a68c6ceb47d.png">  
  
After Calling response_parser.put val_ changes to 14 with getMessage returning "Bad version"

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-11-16 03:44:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969824621  

Well, step in to `response_parser.put` and you will quickly get to `basic_parser_base::parse_version` which is where that error can come from. I would bet dollars to donuts that the buffer does not contain what you expect.

---

## Comment 6

> Username: benstadin  
> Created at: 2021-11-16 03:50:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969831749  

I'm still puzzled where it's going wrong. When entering the callback, the buffer is clearly containing a 401, yet status code returned by parseRes.result_int() is 200.  
  
<img width="1465" alt="image" src="https://user-images.githubusercontent.com/1433419/141892523-938c522c-6858-4b23-b6aa-af8692155710.png">

---

## Comment 7

> Username: benstadin  
> Created at: 2021-11-16 03:54:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969833920  

The error message from the unaltered ec.messsage() is here "Unexpected body" when entering the callback. That makes kind of sense, because it does not contain a body here:  
  
http::response<http::empty_body> proxyRes_;  
  
Nginx actually delivers a body as part of it's response. But as far as I know, this is not to be expected.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-11-16 03:55:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969834349  

if `ec` indicates an error, the contents of the parsed response are unspecified (@madmongo1 the documentation should make this clear)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-11-16 03:56:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969834711  

You can't use `empty_body` if you are not 100% certain that there will be no body (for example, in the response to a HEAD request).

---

## Comment 10

> Username: benstadin  
> Created at: 2021-11-16 04:09:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-969839994  

Thanks! That was it indeed.   
  
The last info is quite valuable. All examples I found were using empty_body (like https://stackoverflow.com/questions/69713739/how-to-connect-with-boostasio-to-a-https-server-using-a-proxy/69715628#69715628). Which made kind of sense for proxying.

---

## Comment 11

> Username: benstadin  
> Created at: 2021-11-16 15:16:43 UTC  
> Updated at: 2021-11-16 15:40:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970374096  

I spoke too early:  
  
- When the proxy is happy with the credentials, or no authentication is required, it will not send a body.   
- If authentication or anything else goes wrong with the proxy, a HTTP body may be present. For this reason parser.skip() needs to be set. Without that the communication would freeze, since a body is expected.    
  
The problem is that with parser_skip(true) set the headers will not be parsed, ec will not contain any error on read, and the status code for the response will remain 200 - even though a valid buffer is present with a status code of 401 or 407. From the documentation for skip() I thought it should only skip parsing the body, but would still parse headers.   
  
I'm trying to figure a proper way to handle this. I'm trying to parse the headers manually, as in the previous code snippet. But the problem here is that I do not see how to access the header data: flat_buffer::data() will return the body data. The actual header data that is upfront is not accessible since begin_ is a private variable and thus manual parsing fails.   
  
Is there another way to access the raw buffer data? Optimally though I think there would be a way to skip waiting for the body but parse headers regardless.   
  
```  
        if (ec) {  
            std::cout << ec.message();  
            return;  
        }  
  
        auto statusCode = proxyRes_.result_int(); // <-- 200 here  
          
        // <-- No headers available here  
        for (auto& h : proxyRes_.base()) {  
            std::cout << "Field: " << h.name() << "/text: " << h.name_string() << ", Value: " << h.value() << "\n";  
        }  
          
      http::parser<false, http::string_body> response_parser;  
      response_parser.eager(true);  
      proxyBuffer_.data(), ec);  // <-- will contain "<html>\r\n<head><title>401 Authorization Required</title>..."  
      auto res2 = response_parser.release();  
      auto code = res2.result_int();  
      auto message = ec.message();  
```

---

## Comment 12

> Username: vinniefalco  
> Created at: 2021-11-16 15:45:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970402652  

Call `eager( false )`?  
https://www.boost.org/doc/libs/1_77_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/eager/overload2.html

---

## Comment 13

> Username: benstadin  
> Created at: 2021-11-16 16:05:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970421823  

Doesn't make a difference. proxyBuffer_.data() would also still only contain the body data, I do not see any public method to get flat_buffer::begin_ which would only there contain the header.

---

## Comment 14

> Username: madmongo1  
> Created at: 2021-11-16 16:15:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970431283  

Do I understand correctly that you want to conditionally read a body, if present?

---

## Comment 15

> Username: benstadin  
> Created at: 2021-11-16 16:30:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970446046  

Actaully, I'm not so much interested in the body. I could for example live with a status code and cancel subsequent communication via the proxy. Though the status code itself is important.   
  
I was trying to use empty_body for the response but that should only be used when a body is never to be expected. For proxy use, this may happen on athentication or other failures.   
  
Even then, when using an empty_body the problem will be vice-versa: I'd get an ec in on_read and would need to attempt a create a parser using a string_body from the buffer which, in order to get the status code.   
  
But I do not see any way to access the buffer's raw bytes which would berequired for creating the parser. buffer.data() only points to the body HTML data but not to the beginning of the received bytes (begin_ is not accessible):  
  
From flat_buffer.hpp:  
```  
const_buffers_type  
    data() const noexcept  
    {  
        return {in_, dist(in_, out_)};  
    }  
```

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-11-16 16:32:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970448192  

I'll see if I can reproduce and come up with a solution for you

---

## Comment 17

> Username: vinniefalco  
> Created at: 2021-11-16 16:33:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970449072  

Why does "buffer.data() only points to the body HTML data"?

---

## Comment 18

> Username: benstadin  
> Created at: 2021-11-16 17:02:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970474479  

My guess is that flat_buffers::consume() was called internally and the headers have been parsed, but the response was not been updated with those headers.   
  
On another note, it seems as calling flat_buffers::shrink_to_fit() removes the header data, preserving just the body:  
  
![image](https://user-images.githubusercontent.com/1433419/142030650-bbfb867a-7b08-4e23-bfbc-df09c3ec14f6.png)  
  
This is the buffer before calling shrink_to_fit, containing the headers as expected:  
  
![image](https://user-images.githubusercontent.com/1433419/142030977-156244fc-96ee-4042-b255-23156436f2cb.png)

---

## Comment 19

> Username: vinniefalco  
> Created at: 2021-11-16 17:09:14 UTC  
> Updated at: 2021-11-16 17:09:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970481649  

Ah yes, `consume` was called. Beast supports reading only the header and inspecting the contents (reading a possibly existing body afterwards) so I'm not sure why you're having this problem. There's even an example which demonstrates this:  
https://www.boost.org/doc/libs/1_77_0/libs/beast/doc/html/beast/more_examples/change_body_type.html  
  
The implementation of `read_header` calls `parser.eager( false )` as I mentioned earlier:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/read.hpp#L507

---

## Comment 20

> Username: benstadin  
> Created at: 2021-11-16 17:50:30 UTC  
> Updated at: 2021-11-16 17:53:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970517465  

I'm not sure how I can adapt this, since I'm using the async API. http::async_read is called in my on_proxy_write method. The next time I see the response the headers are not available when skip(true) is set and the buffer not accessible:  
  
```  
        // <-- No headers available here  
        for (auto& h : proxyRes_.base()) {  
            std::cout << "Field: " << h.name() << "/text: " << h.name_string() << ", Value: " << h.value() << "\n";  
        }  
```  
  
Do I miss something?  
  
EDIT:  
  
There is async_read_header. Trying.

---

## Comment 21

> Username: benstadin  
> Created at: 2021-11-16 18:31:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2343#issuecomment-970554389  

Thanks, that seems to do the trick.  
  
From a user perspective I'd wish it was possible to access the raw buffer, even though I do not need this now anymore here.
