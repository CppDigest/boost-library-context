# #2173 - How to use a new body instance after received the HTTP header [Closed]

> Username: ginoblue  
> Created at: 2021-02-26 10:11:35 UTC  
> Updated at: 2021-03-01 01:21:55 UTC  
> Closed at: 2021-03-01 01:21:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2173  

env: boost 1.75-centos7-scl-gcc 9.3.1  
i'm writing a http server using beast, i use the string_body to receive the request,  
but sometime, i need to handle a request that is posting file to server, file size can be 5GB，  
so i should use the file_body.  
Unfortunately, the class parser not support create new instance after receive the header, or i can override the on_body_init function.  
because the parser is a template class.  
So, what can i do?  
  
Forgive me about the format i'm writing with my bad English. I thinks you guys must understand what i saying..  
Boost is so elegant， i don't want to destroy it with my suck c++ skill

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-02-26 10:40:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2173#issuecomment-786565003  

I don't fully understand what you want to do.  
Are you saying that you want to check the header before you allow the parsing of the body into the file?  
  
If this is the case you can split the parsing into 2 phases:  
  
First, parse the header:  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html  
  
Then, while parser.is_done() != true, parse the body:  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html  
  
You can speecialise the response_parser template class to indicate that you wish to use a file_body if that is what you want to do:  
https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__response_parser.html

---

## Comment 2

> Username: ginoblue  
> Created at: 2021-02-27 13:47:50 UTC  
> Updated at: 2021-02-27 13:52:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2173#issuecomment-787075393  

> 我不完全了解您想做什么。  
> 您是说要在允许将正文解析到文件之前检查标头吗？  
>   
> 在这种情况下，您可以将解析分为两个阶段：  
>   
> 首先，解析标头：[https](https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html) :  
> [//www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html](https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html)  
>   
> 然后，当parser.is_done（）！= true时，解析正文：[https](https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html) ://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html  
> [](https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read_header.html)  
>   
> 您可以专门化response_parser模板类，以表明您希望使用file_body，如果您要这样做：[https](https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__response_parser.html) ://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast  
> [/ref/boost__beast__http__response_parser.html](https://www.boost.org/doc/libs/1_75_0/libs/beast/doc/html/beast/ref/boost__beast__http__response_parser.html)  
  
Thank you so much for your quick response!! Though you not understand what i was meaning, but following your guide i have solved out the problem. i wirte the code like:  
parser<true, string_body, Fields> p;  
async_read_header(...p...);  
when i got the resquest which is posting a file to server. i can:  
first, create the file on server then,  
parser<true, file_body, Fields> p2(std::move(p));  
async_read(...p2...);  
  
but !! it looks like weird...  
my original intention may be this code:  
parser<true, BODY_UNKNOWN, Fields> p;  
async_read_header(...p...);  
create the xxx_body based on different request,  
async_read remaining body.
