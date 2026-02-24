# #1776 - SSL through proxy, "handshake: wrong version number" [Closed]

> Username: Gomox11  
> Created at: 2019-12-03 12:05:24 UTC  
> Updated at: 2020-01-09 17:28:49 UTC  
> Closed at: 2020-01-09 17:28:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1776  

Version of Beast : 189  
  
Hello everyone,  
  
I'm trying to establish a TLS connection through my proxy server.  
  
Setting up the tunnel using HTTP CONNECT works as expected. The code I use as a demo is:  
  
```cpp  
net::io_context ioc{};  
  
std::thread([&] {  
    ioc.run();  
 }).detach();  
  
ssl::context ssl_context_{ssl::context::tls};  
ssl_context_.set_default_verify_paths();  
ssl_context_.add_verify_path("/opt/misc/certificates");  
  
auto websocket_secure_ = std::make_shared<websocket::stream<ssl::stream<tcp::socket>>>(ioc, ssl_context_);  
  
tcp::resolver resolver_{ioc};  
auto const resolve_results = resolver_.resolve("www-my.proxy.int", "8080");  
net::connect(websocket_secure_->next_layer().next_layer(), resolve_results.begin(), resolve_results.end());  
  
const std::string HOST_TO_CONNECT_TO = "my.host.com:443";  
  
http::request<http::string_body> request_connect{http::verb::connect, HOST_TO_CONNECT_TO, 11};  
request_connect.insert(http::field::proxy_authorization, "Basic dXNlcm5hbWU6cGFzc3dvcmQ=");  
request_connect.insert(http::field::host, HOST_TO_CONNECT_TO);  
boost::beast::http::write(websocket_secure_->next_layer().next_layer(), request_connect);  
```  
As expected, Wireshark shows a HTTP CONNECT followed by a 200 response.  
  
However, attempting to do the SSL handshake afterwards results in a thrown error: "handshake: wrong version number".  
  
```cpp  
try {  
    websocket_secure_->next_layer().handshake(ssl::stream_base::client);  
  } catch (boost::system::system_error& error) {  
    std::cout << error.what();  
    throw error;  
  }  
```  
  
Wireshark shows a Client Hello, followed by a Server Hello, Certificate, Server Key Exchange, Certificate Request, Server Hello Done. The trace looks fine to me and doesn't differ from a request of my browser to that same website.  
  
The SSL handshake works just fine if I don't have to tunnel the requests through my proxy.  
  
Why am i getting the thrown error inside my application? Thanks in advance for any help!

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-03 14:51:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561201973  

You could simplify some of that code to  
```  
net::connect(  
    beast::get_lowest_layer(websocket_secure_),  
    resolver_.resolve("www-my.proxy.int", "8080"));  
```  
  
> Why am i getting the thrown error inside my application  
  
Where are you reading the HTTP response from the proxy?

---

## Comment 2

> Username: Gomox11  
> Created at: 2019-12-03 15:15:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561212725  

> Where are you reading the HTTP response from the proxy?  
  
I'm currently not reading it inside the application but only tracing it through wireshark.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-12-03 15:17:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561213723  

Well, that's why your program is failing. The SSL implementation is expecting to see a TLS record but instead, it is seeing the HTTP response from the proxy. You have to read the responds from the stream before you perform the TLS handshake.

---

## Comment 4

> Username: Gomox11  
> Created at: 2019-12-03 15:22:00 UTC  
> Updated at: 2019-12-03 15:25:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561215789  

Do I need to use the async calls for the read? Because I think I tried that earlier and the application was stuck on the read command.  
  
```cpp  
boost::beast::http::write(websocket_secure_->next_layer().next_layer(), request_connect);  
// ...  
beast::flat_buffer buffer;  
http::response<http::dynamic_body> res;  
boost::beast::http::read(websocket_secure_->next_layer().next_layer(), buffer, res);  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-12-03 15:32:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561220754  

You should not need async calls. If the application is stuck, you need to step in and figure out why.

---

## Comment 6

> Username: Gomox11  
> Created at: 2019-12-03 15:34:08 UTC  
> Updated at: 2019-12-03 15:34:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561221314  

I was using the approach you suggested, calling the http::read posted above right after writing the CONNECT request to the proxy server. However, read never returns, so I'm wondering if I might be using the wrong arguments to that function call? I was expecting to read back the 200 code.  
  
```cpp  
  http::request<http::string_body> request_connect{http::verb::connect, HOST_TO_CONNECT_TO, 11};  
  request_connect.insert(http::field::proxy_authorization, PROXY_AUTHENTICATION_CREDENTIALS);  
  request_connect.insert(http::field::host, HOST_TO_CONNECT_TO);  
  request_connect.insert(http::field::proxy_connection, "keep-alive");  
  request_connect.insert(http::field::connection, "keep-alive");  
  boost::beast::http::write(websocket_secure_->next_layer().next_layer(), request_connect);  
  
  std::cout << "Reading...\n";  
  beast::flat_buffer buffer{};  
  http::response<http::dynamic_body> res{};  
  boost::beast::http::read(websocket_secure_->next_layer().next_layer(), buffer, res);  
  // Application never reaches 'DONE'  
  std::cout << "DONE!\n";  
```

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-12-03 15:34:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561221647  

Try `read_header` instead and see what happens

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-12-03 15:35:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561222053  

Can you print the entire response you are seeing in wireshark?

---

## Comment 9

> Username: Gomox11  
> Created at: 2019-12-03 15:39:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561223916  

> Try `read_header` instead and see what happens  
  
I'll have to quickly dive into the syntax of that call.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-12-03 15:41:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561224803  

Nevermind that, lets see the wireshark of the proxy's HTTP response.

---

## Comment 11

> Username: Gomox11  
> Created at: 2019-12-03 15:44:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561226008  

![image](https://user-images.githubusercontent.com/32459794/70065887-165ed400-15ec-11ea-8083-e7bb61d72315.png)

---

## Comment 12

> Username: vinniefalco  
> Created at: 2019-12-03 15:47:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561227495  

Ahh... I see. You have to tell Beast that you are not expecting a body, otherwise Beast has no way to know that you are reading the response to a CONNECT request. See:  
https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/skip/overload2.html

---

## Comment 13

> Username: Gomox11  
> Created at: 2019-12-03 15:50:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561228990  

> Ahh... I see. You have to tell Beast that you are not expecting a body, otherwise Beast has no way to know that you are reading the response to a CONNECT request. See:  
> https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/skip/overload2.html  
  
Ahh I see, that would've taken me ages to find :D Thank you very much - it's working as intended (for now) :)   
  
```cpp  
 http::request<http::string_body> request_connect{http::verb::connect, HOST_TO_CONNECT_TO, 11};  
  request_connect.insert(http::field::proxy_authorization, PROXY_AUTHENTICATION_CREDENTIALS);  
  request_connect.insert(http::field::host, HOST_TO_CONNECT_TO);  
  request_connect.insert(http::field::proxy_connection, "keep-alive");  
  request_connect.insert(http::field::connection, "keep-alive");  
  boost::beast::http::write(websocket_secure_->next_layer().next_layer(), request_connect);  
  
  http::response<http::empty_body> res;  
  http::parser<false, http::empty_body> p(res);  
  p.skip(true);  
  boost::beast::flat_buffer buffer;  
  http::read(websocket_secure_->next_layer().next_layer(), buffer, p);  
```

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-12-03 15:53:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561230601  

Remember that you can replace  
```  
websocket_secure_->next_layer().next_layer()  
```  
with  
```  
beast::get_lowest_layer( *websocket_secure_ )  
```

---

## Comment 15

> Username: Gomox11  
> Created at: 2019-12-03 15:55:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-561231182  

> Remember that you can replace  
>   
> ```  
> websocket_secure_->next_layer().next_layer()  
> ```  
>   
> with  
>   
> ```  
> beast::get_lowest_layer( *websocket_secure_ )  
> ```  
  
I'll keep that in mind :)

---

## Comment 16

> Username: stale[bot]  
> Created at: 2020-01-02 17:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-570275145  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 17

> Username: stale[bot]  
> Created at: 2020-01-09 17:28:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1776#issuecomment-572667401  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
