# #2186 - Add prepare_payload() to sync http example [Closed]

> Username: mjs476  
> Created at: 2021-03-12 20:44:07 UTC  
> Updated at: 2024-06-06 05:34:22 UTC  
> Closed at: 2022-09-24 05:27:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2186  

### Version of Beast  
  
```  
BOOST_BEAST_VERSION 181  
```  
  
### Steps necessary to reproduce the problem  
  
I am using the Beast HTTP client to connect to a [civetweb](https://github.com/civetweb/civetweb) HTTP server. The server expects a HTTP post and will just send a response with the post payload.  
  
The Beast client looks like this:  
  
```c++  
template <typename Body_>  
http::response<http::string_body> executeBeast(const http::request<Body_>& request_)  
{  
    // See: https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/quick_start.html#beast.quick_start.http_client  
    net::io_context ioc;  
    tcp::resolver resolver(ioc);  
    tcp::socket socket(ioc);  
  
    auto resolved = resolver.resolve("localhost", address.port());  
    net::connect(socket, resolved.begin(), resolved.end());  
  
    // See: https://github.com/boostorg/beast/issues/2042#issuecomment-666161772.  
    socket.set_option(tcp::no_delay(true));  
  
    http::write(socket, request_);  
  
    beast::flat_buffer buffer;  
    http::response<http::string_body> response;  
    http::read(socket, buffer, response);  
  
    beast::error_code ec;  
    socket.shutdown(tcp::socket::shutdown_both, ec);  
  
    return response;  
}  
```  
  
And the equivalent libcurl call:  
  
```c++  
std::string executeCurl(const std::string& target_, const std::string& payload_)  
{  
    CURL* curl = curl_easy_init();  
    PDT_CHECK_NE(curl, nullptr);  
    PDT_ON_BLOCK_EXIT([&] { curl_easy_cleanup(curl); });  
  
    std::string url = "http://localhost:" + address.port() + target_;  
    curl_easy_setopt(curl, CURLOPT_URL, url.c_str());  
    curl_easy_setopt(curl, CURLOPT_POSTFIELDS, payload_.c_str());  
  
    std::string payload;  
    auto writeFn = +[](void *contents, size_t size, size_t nmemb, void *userp)  
    {  
        ((std::string*)userp)->append((char*)contents, size * nmemb);  
        return size * nmemb;  
    };  
    curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, writeFn);  
    curl_easy_setopt(curl, CURLOPT_WRITEDATA, &payload);  
  
    CURLcode res = curl_easy_perform(curl);  
    PDT_CHECK_MESSAGE(res == CURLE_OK,  "HTTP request to " + url + " failed with error: " + curl_easy_strerror(res));  
  
    return payload;  
}  
```  
  
When I compare the two, libcurl is about 1000x faster:  
  
```c++  
std::string body = "Hello World!";  
  
auto start = system_clock::now();  
std::cout << "executeCurl: " << executeCurl("/echo", body)  
            << "Took " << deltaMs(start) << " ms" << std::endl;  
  
http::request<http::string_body> request{http::verb::post, "/echo", HttpVersion};  
request.body() = body.c_str();  
start = system_clock::now();  
std::cout << "executeBeast: " << executeBeast(request).body()  
            << "Took " << deltaMs(start) << " ms" << std::endl;  
```  
  
The result:  
  
```  
executeCurl: Hello World!  
took 112 ms  
executeBeast: Hello World!  
took 90236 ms  
```  
  
I am guessing that I am configuring Beast HTTP wrong -- it should not ordinarily take 90s to transfer mere kilobytes back and forth.  
  
If I run with gdb, it seems like we are spending all of the time in this backtrace:  
  
```  
#0  0x00007fffec800bad in recvmsg () at ../sysdeps/unix/syscall-template.S:81  
#1  0x00000000006c57a1 in boost::asio::detail::socket_ops::recv (s=17, bufs=0x7fffffffc240, count=1, flags=0, ec=...) at include/boost/asio/detail/impl/socket_ops.ipp:784  
#2  0x00000000006c5878 in boost::asio::detail::socket_ops::sync_recv (s=17, state=16 '\020', bufs=0x7fffffffc240, count=1, flags=0, all_empty=false, ec=...) at include/boost/asio/detail/impl/socket_ops.ipp:811  
#3  0x00000000006dc2f2 in boost::asio::detail::reactive_socket_service_base::receive<boost::asio::mutable_buffer> (this=0x1c3d2c8, impl=..., buffers=..., flags=0, ec=...) at include/boost/asio/detail/reactive_socket_service_base.hpp:315  
#4  0x00000000006d872d in boost::asio::basic_stream_socket<boost::asio::ip::tcp>::read_some<boost::asio::mutable_buffer> (this=0x7fffffffc660, buffers=..., ec=...) at include/boost/asio/basic_stream_socket.hpp:854  
#5  0x00000000006d5912 in boost::beast::http::read_some<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::parser<false, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char> > > (stream=..., buffer=..., parser=..., ec=...) at include/boost/beast/http/impl/read.ipp:528  
#6  0x00000000006d3418 in boost::beast::http::read<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::parser<false, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char> > > (stream=..., buffer=..., parser=..., ec=...) at include/boost/beast/http/impl/read.ipp:718  
#7  0x00000000006d09cb in boost::beast::http::read<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char> > (stream=..., buffer=..., msg=..., ec=...) at include/boost/beast/http/impl/read.ipp:808  
#8  0x00000000006cd2e0 in boost::beast::http::read<boost::asio::basic_stream_socket<boost::asio::ip::tcp>, boost::beast::basic_flat_buffer<std::allocator<char> >, false, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, std::allocator<char> > (stream=..., buffer=..., msg=...) at include/boost/beast/http/impl/read.ipp:779  
```  
  
  
### All relevant compiler information  
  
Boost compiled with GCC 8.1, using shared libraries. Running on RHEL 7.8.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-03-12 20:57:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797750443  

How long does the DNS resolution take?

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-03-12 20:58:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797751045  

Q2: is the request http1.0 or 1.1?   
I see you’re doing the shutdown after receiving the response. This would be incorrect for http1.0.

---

## Comment 3

> Username: mjs476  
> Created at: 2021-03-12 21:05:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797755273  

@vinniefalco: The client and server are both on the same machine -- client is sending to localhost. Is DNS relevant in this case?  
  
@madmongo1: HTTP 1.1. Forgot to include the following line of code:  
```c++   
constexpr int HttpVersion = 11; // i.e. version 1.1.  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-03-12 21:09:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797756736  

Yes, DNS is relevant. Try changing `executeBeast` to use this:  
```  
auto resolved = resolver.resolve( "127.0.0.1", address.port() );  
```  
  
When using "localhost", it performs a blocking system call. But if the string is a legal IPv4 address, then the system call is not made.

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-03-12 21:09:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797757058  

Can you share the code where you build the request please?  
I want to check that the request has been prepared, which would set its content-length and thus make it a correct HTTP/1.1 request.

---

## Comment 6

> Username: mjs476  
> Created at: 2021-03-12 21:18:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797760872  

@vinniefalco: Ah yes, the `localhost` resolution. It's still slow with `127.0.0.1`.  
  
@madmongo1: The server is not currently sending content-length. Instead, it sends connection: close.

---

## Comment 7

> Username: madmongo1  
> Created at: 2021-03-12 21:21:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797762300  

> Can you share the code where you build the request please?  
  
I mean on the client. I suspect that you have not prepared the request and therefore it has no content length.  
That would mean the server would be waiting for an end of stream, which never arrives.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2021-03-12 21:22:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797762696  

90 seconds is suspiciously equal to an OS-provided synchronous socket default timeout :)

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-03-12 21:22:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797762982  

Try adding  
```  
request_.prepare_payload();  
```  
before you write the request.

---

## Comment 10

> Username: mjs476  
> Created at: 2021-03-12 21:23:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797763294  

@madmongo1 Here is an example request/response using the commandline curl  
  
```  
% curl -v -d "Hello World" 127.0.0.1:2043/echo  
*   Trying 127.0.0.1...  
* Connected to 127.0.0.1 (127.0.0.1) port 2043 (#0)  
> POST /echo HTTP/1.1  
> Host: 127.0.0.1:2043  
> User-Agent: curl/7.48.0  
> Accept: */*  
> Content-Length: 11  
> Content-Type: application/x-www-form-urlencoded  
>  
* upload completely sent off: 11 out of 11 bytes  
< HTTP/1.1 200 OK  
< Content-Type: text  
< Connection: close  
<  
Hello World  
* Closing connection 0  
```

---

## Comment 11

> Username: mjs476  
> Created at: 2021-03-12 21:26:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797764543  

@vinniefalco `prepare_payload()` did the trick! Care to explain? If this is needed, can this be added to the examples like https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/quick_start.html#beast.quick_start.http_client

---

## Comment 12

> Username: madmongo1  
> Created at: 2021-03-12 21:29:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797765914  

He's correct. The `prepare_payload()` is missing from the example in the latest release.

---

## Comment 13

> Username: madmongo1  
> Created at: 2021-03-12 21:30:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797766674  

Thanks for reporting this @mjs476 .  
  
It's an oversight in our documentation.

---

## Comment 14

> Username: mjs476  
> Created at: 2021-03-12 21:31:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797766899  

Thanks so much for the quick help and fix! I'm new to Beast HTTP and I much prefer it to libcurl :)

---

## Comment 15

> Username: madmongo1  
> Created at: 2021-03-12 21:32:42 UTC  
> Updated at: 2021-03-12 21:34:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-797767653  

You're welcome. Dont' forget to give us our github star! :point_up:

---

## Comment 16

> Username: ecorm  
> Created at: 2023-10-09 02:23:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2186#issuecomment-1752264613  

@madmongo1 , I'm confused about when `prepare_payload` is actually needed. In this [issue comment](https://github.com/boostorg/beast/issues/524#issuecomment-310132434), @vinniefalco  says that calling it is not needed, whereas in this issue, neglecting to call it results in 1000x slower code.
