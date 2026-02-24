# #2304 - How do I obtain field-value pairs from the query string? [Closed]

> Username: bangusi  
> Created at: 2021-08-27 16:08:27 UTC  
> Updated at: 2022-06-16 14:32:50 UTC  
> Closed at: 2022-06-16 14:32:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2304  

How do I obtain field-value pairs from the query string?  
None of the examples show how to parse a query string.  
I the library capable of processing a request generated from an html form or not?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-08-27 16:37:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2304#issuecomment-907330285  

> How do I obtain field-value pairs from the query string?  
  
You would use the `target()` method on the request object and tokenise the returned string using delimiters `&` and `?`.  
  
For header fields there is built-in support  
Like this:  
```  
#include <boost/beast/http.hpp>  
#include <iostream>  
  
void test(boost::beast::http::request<boost::beast::http::string_body> const& req)  
{  
    std::cout << "iteration:\n";  
    for (auto&& f : req)  
    {  
        std::cout << f.name_string() << ": " << f.value() << "\n";  
    }  
  
    std::cout << "\ndirect access:\n";  
    std::cout << "Content-Type: " << req["Content-Type"] << "\n";  
    std::cout << "Nonexistent: " << req["Nonexistent"] << "\n";  
  
}  
  
int main()  
{  
    boost::beast::http::request<boost::beast::http::string_body> req;  
    req.set("Content-Type", "text/plain");  
    req.set("X-Custom-Header", "abcdef");  
  
    test(req);  
}  
```  
  
https://godbolt.org/z/zK6x3oE4x  
  
> None of the examples show how to parse a query string.  
  
Parsing the query string is out of scope for Beast, which is a low level library. Boost.URL (in development) is designed to solve this problem. In the meantime, a trivial string tokenisation will do it.  
  
> Is the library capable of processing a request generated from an html form or not?  
The library will present form data to you. in the body() of the request. It is out of scope for Beast to parse form data, although this is fairly trivial with standard C++ string processing.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2304#issuecomment-1008220744  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-06-16 14:32:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2304#issuecomment-1157730838  

You could try this library: https://github.com/CPPAlliance/url
