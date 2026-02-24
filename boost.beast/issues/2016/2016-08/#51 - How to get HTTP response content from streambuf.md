# #51 - How to get HTTP response content from streambuf ? [Closed]

> Username: salutant  
> Created at: 2016-08-17 04:59:32 UTC  
> Updated at: 2016-08-22 09:21:21 UTC  
> Closed at: 2016-08-22 09:21:21 UTC  
> Url: https://github.com/boostorg/beast/issues/51  

Can it auto parse http response and separate headers and content

---

## Comment 1

> Username: vinniefalco  
> Created at: 2016-08-17 10:25:04 UTC  
> Url: https://github.com/boostorg/beast/issues/51#issuecomment-240373246  

I'm not sure I understand the question. Are you saying that you have a `boost::asio::streambuf` (or `beast::streambuf`) and you want to parse the HTTP message? Yes, Beast can do this:  
  
```  
using namespace beast;  
  
template<class Body, class ConstBufferSequence>  
response_v1<Body, headers>  
do_parse(ConstBufferSequence const& cb, error_code &ec)  
{  
    error_code ec;  
    parser_v1<false, Body, headers> p;  
    p.write(cb, ec);  
    return p;  
}  
  
void parse_streambuf()  
{  
    error_code ec;  
    boost::asio::streambuf sb;  
    ...  
    std::cout << do_parse(sb.data(), ec);  
}  
```

---

## Comment 2

> Username: salutant  
> Created at: 2016-08-18 03:15:01 UTC  
> Url: https://github.com/boostorg/beast/issues/51#issuecomment-240613123  

Sorry, my english is poor . I want to say from the example i don't known how to get the response headers and body directly .

---

## Comment 3

> Username: salutant  
> Created at: 2016-08-18 07:00:55 UTC  
> Url: https://github.com/boostorg/beast/issues/51#issuecomment-240640491  

Is beast::streambuf compatible with boost::asio::streambuf   
I cannot use boost::asio::buffer_cast<const char *>() to convert a streambuf to a raw pointer

---

## Comment 4

> Username: vinniefalco  
> Created at: 2016-08-18 11:06:23 UTC  
> Updated at: 2016-08-18 11:06:36 UTC  
> Url: https://github.com/boostorg/beast/issues/51#issuecomment-240692543  

Given  
  
```  
beast::http::response_v1<beast::http::string_body, beast::http::headers> res;  
```  
  
You can access the headers using `res.headers`. For example `res.headers["Server"]` will return a string.  
  
The body can be accessed with `res.body`, which in this example will be of type `std::string`.  
  
`beast::streambuf` has the same interface as `boost::asio::streambuf`. You cannot use `buffer_cast` on a `streambuf` or on `streambuf::data`. If you want to convert the entire streambuf to a pointer you need to loop over all the buffers and append them together. The function `beast::to_string` does this, you can use that function or you can write your own. Example:  
  
```  
beast::streambuf sb;  
...  
std::cout << beast::to_string(sb.data());  
```  
  
This is the implementation of `to_string`:  
https://github.com/vinniefalco/Beast/blob/master/include/beast/core/to_string.hpp#L38  
  
Hope this helps!

---

## Comment 5

> Username: salutant  
> Created at: 2016-08-18 13:33:10 UTC  
> Updated at: 2016-08-18 13:49:15 UTC  
> Url: https://github.com/boostorg/beast/issues/51#issuecomment-240723824  

Thanks .  
  
``` C++  
    boost::asio::ip::tcp::socket sock(ios);  
    beast::http::request_v1<beast::http::empty_body> req;  
    req.method = "GET";  
    req.url = "/";  
    req.version = 11;  
    req.headers.replace("Host", "www.boost.org");  
    req.headers.replace("User-Agent", "Beast");  
    beast::http::prepare(req);  
    beast::http::write(sock, req);  
  
    std::string sb;  
    beast::http::response_v1<beast::http::string_body> resp;  
    beast::http::read(sock, sb, resp);  
```  
  
The code  use VC2015 UP3 compile failed with   
error C2338: DynamicBuffer requirements not met

---

## Comment 6

> Username: vinniefalco  
> Created at: 2016-08-18 15:12:01 UTC  
> Url: https://github.com/boostorg/beast/issues/51#issuecomment-240754628  

The second argument to `http::read` must meet the requirements of **DynamicBuffer** (http://vinniefalco.github.io/beast/beast/types/DynamicBuffer.html). Both `boost::asio::streambuf` and `beast::streambuf` meet these requirements. This should fix your code:  
  
```  
    beast::streambuf sb;  
    beast::http::response_v1<beast::http::string_body> resp;  
    beast::http::read(sock, sb, resp);  
```  
  
The body of the message will be in `resp.body`, of type `std::string`.  
  
Note that if you want to read another response on the same connected socket, you have to re-use the same streambuf.
