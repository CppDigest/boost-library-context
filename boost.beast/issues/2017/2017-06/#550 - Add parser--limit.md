# #550 - Add parser::limit [Closed]

> Username: vinniefalco  
> Created at: 2017-06-27 23:21:48 UTC  
> Updated at: 2018-04-12 06:07:01 UTC  
> Closed at: 2017-06-28 21:13:58 UTC  
> Url: https://github.com/boostorg/beast/issues/550  

From pre-Boost-review feedback

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-28 16:50:23 UTC  
> Url: https://github.com/boostorg/beast/issues/550#issuecomment-311720265  

Added `body_limit` and `header_limit`

---

## Comment 2

> Username: scontini76  
> Created at: 2018-04-11 14:51:41 UTC  
> Url: https://github.com/boostorg/beast/issues/550#issuecomment-380480891  

Hi Vinnie, sorry it's not clear to me the simplest way to customise the body limits, at the moment I'm reading data in a very easy way:   
  
`   
boost::beast::flat_buffer buffer;  
http::response<http::string_body> res;  
http::read(socket, buffer, res);  
 `  
But my payload exceeded 8MiB. It seems that I need to define a custom parser, does I really need to do this? and suppose I do so, how my custom parser (as described in https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__http__basic_parser.html) fits in my code?  
  
I successfully used your great library in many standard situation, but I struggled a bit in those kind of customisation and I fear to follow too much complex way to just increase a buffer.  
  
Thank you for your time!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-04-11 15:29:14 UTC  
> Url: https://github.com/boostorg/beast/issues/550#issuecomment-380494521  

The documentation explains how to use the parser-oriented interfaces and provides example code:  
https://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_http/parser_stream_operations.html

---

## Comment 4

> Username: scontini76  
> Created at: 2018-04-12 06:07:01 UTC  
> Url: https://github.com/boostorg/beast/issues/550#issuecomment-380689357  

Yeah, sorry you are right, I missed that page, I reached my goal this way:  
  
```c++  
boost::beast::flat_buffer buffer;  
http::response_parser<http::string_body> parser;  
parser.body_limit(16 * 1024 * 1024);  
http::read(socket, buffer, parser);  
std::cout << parser.get().body() << "\n";  
```  
  
thank you again
