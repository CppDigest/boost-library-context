# #2110 - How to access response status code from a response_parser? [Closed]

> Username: rhegner  
> Created at: 2020-10-21 15:28:51 UTC  
> Updated at: 2020-10-22 20:55:40 UTC  
> Closed at: 2020-10-22 20:55:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2110  

I'm using `http::async_read_header` to read only the header of the response into a `http::response_parser<http::empty_body>`. I'm able to access all the headers but I couldn't figure out how to access the response status code.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-10-21 15:37:25 UTC  
> Updated at: 2020-10-21 15:58:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2110#issuecomment-713665939  

Hi @rhegner ,  
  
The response_parser has the method `get()` which can be used to access the embedded reponse.  
  
```  
void test(http::response_parser<http::empty_body> const& p)  
{  
    http::response<http::empty_body> const& msg = p.get();  
  
    // text reason for failure  
    boost::beast::string_view reason = msg.reason();  
  
    // status code enum  
    http::status result = msg.result();  
  
    // status code as integer  
    int result_int = msg.result_int();  
}  
```

---

## Comment 2

> Username: madmongo1  
> Created at: 2020-10-21 16:09:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2110#issuecomment-713687185  

Vinnie mentioned that there is a comprehensive email/mime library here:  
http://www.codesink.org/mimetic_mime_library.html#docs  
  
It might be worth a look if you're under time pressure.

---

## Comment 3

> Username: rhegner  
> Created at: 2020-10-22 20:55:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2110#issuecomment-714756980  

`p.get().result_int()` did the trick, thank you!
