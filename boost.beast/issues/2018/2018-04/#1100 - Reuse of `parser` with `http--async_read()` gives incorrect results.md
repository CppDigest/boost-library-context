# #1100 - Reuse of `parser` with `http::async_read()` gives incorrect results [Closed]

> Username: smipi1  
> Created at: 2018-04-18 20:26:23 UTC  
> Updated at: 2018-12-07 00:44:34 UTC  
> Closed at: 2018-12-07 00:44:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1100  

Reusing a `parser` with `http::async_read()` results in `parser.body()` accumulating the bodies of all the responses. Calling `parser.clear()` before again calling `http::async_read()` does not fix this. The only solution appears to be to create a new `parser` with every `http::async_read()` performed.  
  
Unlike the [http::read documentation](https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__read/overload3.html) on `msg`, the [http::async_read documentation](https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload1.html) makes no mention that the `parser` should not have any previous content.  
  
The [http_crawl.cpp example](https://www.boost.org/doc/libs/1_67_0/libs/beast/example/http/client/crawl/http_crawl.cpp) also happily reuses the `http::response<http::string_body> res_` member of the `worker` class for every request. For [http_crawl.cpp](https://www.boost.org/doc/libs/1_67_0/libs/beast/example/http/client/crawl/http_crawl.cpp), this might not be problematic because it ignores the response body, but it creates the wrong expectations in the absence of documentation warning against such use.  
  
### Version of Beast  
  
144  
  
### Steps necessary to reproduce the problem  
  
1. Modify [http_crawl.cpp](https://www.boost.org/doc/libs/1_67_0/libs/beast/example/http/client/crawl/http_crawl.cpp) to dump `res_.body()` in `worker::on_read()`. E.g.:  
    ```  
    ...  
    auto const code = res_.result_int();  
    std::cout << "============================" << std::endl;  
    std::cout << res_.body() << std::endl;  
    std::cout << "----------------------------" << std::endl;  
    report_.aggregate(  
    ...  
    ```  
1. Modify [urls_large_data.cpp](https://www.boost.org/doc/libs/1_67_0/libs/beast/example/http/client/crawl/urls_large_data.cpp) to fetch only `google.com` two times:  
    ```  
    ...  
    urls_large_data()  
    {  
        static std::vector <char const*> const urls ({  
            "google.com",  
            "google.com"  
        });  
      
        return urls;  
    }  
    ...  
    ```  
1. Recompile with `bjam`  
1. Run with 1 thread: `http-crawl 1`  
    ```  
    ============================  
    <HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">  
    <TITLE>301 Moved</TITLE></HEAD><BODY>  
    <H1>301 Moved</H1>  
    The document has moved  
    <A HREF="http://www.google.com/">here</A>.  
    </BODY></HTML>  
      
    ----------------------------  
    Progress: 0 of 2  
    ============================  
    <HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">  
    <TITLE>301 Moved</TITLE></HEAD><BODY>  
    <H1>301 Moved</H1>  
    The document has moved  
    <A HREF="http://www.google.com/">here</A>.  
    </BODY></HTML>  
    <HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">  
    <TITLE>301 Moved</TITLE></HEAD><BODY>  
    <H1>301 Moved</H1>  
    The document has moved  
    <A HREF="http://www.google.com/">here</A>.  
    </BODY></HTML>  
      
    ----------------------------  
    Elapsed time:    5 seconds  
    Crawl report  
       Failure counts  
           Timer   : 0  
           Resolve : 0  
           Connect : 0  
           Write   : 0  
           Read    : 0  
           Success : 2  
       Status codes  
           301: 2 (Moved Permanently)  
    ```  
  
### All relevant compiler information  
  
gcc (Ubuntu 7.2.0-8ubuntu3.2) 7.2.0

---

## Comment 1

> Username: smipi1  
> Created at: 2018-04-18 20:38:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1100#issuecomment-382521560  

Hi @vinniefalco,  
  
Is this a documentation/example or an implementation bug?  
  
Regards,  
@smipi1

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-04-18 20:59:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1100#issuecomment-382527396  

>Is this a documentation/example or an implementation bug?  
  
Documentation. The parser was never meant to be reusable. Note that the `message` class has no "clear" member function.

---

## Comment 3

> Username: smipi1  
> Created at: 2018-04-18 21:35:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1100#issuecomment-382536944  

Is it worthwhile reworking the examples as well to make this clear. Minimally a comment why this generally is I'll advised, but okay for the crawler use case.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-04-18 23:20:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1100#issuecomment-382559263  

Actually that might explain why the crawler malfunctions towards the end... good find :)

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-05-18 23:28:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1100#issuecomment-390357415  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: smipi1  
> Created at: 2018-05-19 21:34:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1100#issuecomment-390434143  

Hi @vinniefalco,  
Should I take a stab at improving the documentation, or would you like this to be accommodated with a fix for the crawler too?

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-05-19 22:37:07 UTC  
> Updated at: 2018-05-19 22:37:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1100#issuecomment-390437197  

The place for the documentation is in `basic_parser`, since it can be used even without calling `async_read` , and exhibit the problem. If you want to try your hand at a fix for either of these problems (or both) I certainly won't mind!
