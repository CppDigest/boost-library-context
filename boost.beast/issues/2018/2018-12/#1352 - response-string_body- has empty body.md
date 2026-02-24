# #1352 - response<string_body> has empty body [Closed]

> Username: mnisjk  
> Created at: 2018-12-05 21:20:14 UTC  
> Updated at: 2018-12-05 21:29:45 UTC  
> Closed at: 2018-12-05 21:29:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1352  

I'm attempting to parse a string http response (returned from libcurl) using a `response_parser<string_body>` object, but the `parser.get().body()` seems to always be empty.  
  
I have a short code snippet the reproduces my issue.  
  
**Expected**: `parser.get().body() = "I'm a little teapot"`  
**Observed**: `parser.get().body() = ""`  
```  
string s =  
    "HTTP/1.1 200 OK\r\n"  
    "Content-Type: text/plain; charset=us-ascii\r\n"  
    "Server: Fake Server\r\n"  
    "Content-Length: 19\r\n"  
    "\r\n"  
    "I'm a little teapot\r\n"  
    "\r\n";  
  
boost::beast::error_code ec;  
boost::beast::http::response_parser<string_body> p;  
  
p.put(boost::asio::buffer(s), ec);  
  
boost::beast::http::response<string_body> r = p.get();  
  
// Prints: Ec=system:0  
cout << "Ec=" << ec << endl;  
  
// I'm able to get response headers  
// Prints: Content-Type=text/plain; charset=us-ascii  
cout << "Content-Type=" << r[field::content_type] << endl;  
  
// But unable to get the response body  
// Prints: body=  
cout << "Body=" << r.body() << endl;  
```  
  
Any insight or tips would be greatly appreciated. I've spent more time than I'd care to admit wrestling with this.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-05 21:26:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1352#issuecomment-444654901  

Given the code above, these asserts should not trigger:  
```  
assert(p.is_header_done());  
assert(! p.is_done()); // means you aren't done reading the message  
assert(! p.eager()); // default is non-eager parsing  
```  
and adding this code before the call to `put`  
```  
p.eager(true);  
```  
should resolve your issue.  
  
See:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser/eager/overload2.html  
  
This is also explained here:  
https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/using_http/buffer_oriented_parsing.html

---

## Comment 2

> Username: mnisjk  
> Created at: 2018-12-05 21:29:31 UTC  
> Updated at: 2018-12-05 21:29:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1352#issuecomment-444656130  

doh! Thanks @vinniefalco - works like a charm.  This is user error, so closing issue.
