# #1584 - any tests for "async_write_header", i get an error when use it [Closed]

> Username: wangjiangw  
> Created at: 2019-04-21 03:36:32 UTC  
> Updated at: 2019-05-31 03:45:16 UTC  
> Closed at: 2019-05-31 03:45:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1584  

i am using beast to implement http chunk demand, in the code, when i use http::wirte_header, the program can work well, but if i replace it with http::async_write_header, the program will crash. next is my code  
![image](https://user-images.githubusercontent.com/7013043/56486672-01c14600-650b-11e9-8be9-0f14f70bd51b.png)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-21 03:37:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-485221975  

This issue is lacking the requested information. Please edit the issue to include the information listed above (and delete the text from the template).

---

## Comment 2

> Username: wangjiangw  
> Created at: 2019-04-22 06:31:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-485339627  

> This issue is lacking the requested information. Please edit the issue to include the information listed above (and delete the text from the template).  
  
sorry about that, i have post issues above, the code by comments works well, but the async_write_header can cause program crash, thank you for answering my question !

---

## Comment 3

> Username: wangjiangw  
> Created at: 2019-04-22 06:33:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-485339969  

> > This issue is lacking the requested information. Please edit the issue to include the information listed above (and delete the text from the template).  
>   
> sorry about that, i have post issues above, the code by comments works well, but the async_write_header can cause program crash, thank you for answering my question !  
  
an error occurs in field.ipp, next is the problem   
![image](https://user-images.githubusercontent.com/7013043/56486831-a5aaf180-650b-11e9-9666-cf49a7130f02.png)

---

## Comment 4

> Username: wangjiangw  
> Created at: 2019-04-22 06:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-485340626  

> This issue is lacking the requested information. Please edit the issue to include the information listed above (and delete the text from the template).  
  
another question, when i send header with chunk setting, can i use a http::response<http::string_body> ? in my case, when i use http::response<http::string_body>, client can not receive the response body string

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-04-22 14:06:58 UTC  
> Updated at: 2019-04-22 14:07:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-485427426  

> the async_write_header can cause program crash  
  
Do the examples work for you?

---

## Comment 6

> Username: wangjiangw  
> Created at: 2019-04-23 07:28:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-485677534  

> > the async_write_header can cause program crash  
>   
> Do the examples work for you?  
  
i have not find enough examples for http::async_write_header，https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/using_http/chunked_encoding.html，this link just show the http::write_header

---

## Comment 7

> Username: cmazakas  
> Created at: 2019-04-23 15:38:12 UTC  
> Updated at: 2019-04-23 15:42:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-485857505  

Hi wangjiangw, I use Beast too.  
  
`http::async_write_header` does work as advertised. Example passing test here:  
https://github.com/LeonineKing1199/foxy/blob/develop/test/session_test.cpp#L150-L185  
  
That code does a lot but here is the Beast call the `session` uses:  
https://github.com/LeonineKing1199/foxy/blob/develop/include/foxy/impl/session/async_write_header.impl.hpp#L45-L57

---

## Comment 8

> Username: wangjiangw  
> Created at: 2019-04-24 02:21:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-486041608  

> Hi wangjiangw, I use Beast too.  
>   
> `http::async_write_header` does work as advertised. Example passing test here:  
> https://github.com/LeonineKing1199/foxy/blob/develop/test/session_test.cpp#L150-L185  
>   
> That code does a lot but here is the Beast call the `session` uses:  
> https://github.com/LeonineKing1199/foxy/blob/develop/include/foxy/impl/session/async_write_header.impl.hpp#L45-L57  
  
thx, i will read it today to check if helpful for me, thank you!

---

## Comment 9

> Username: stale[bot]  
> Created at: 2019-05-24 03:04:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-495455772  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 10

> Username: stale[bot]  
> Created at: 2019-05-31 03:45:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1584#issuecomment-497564600  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
