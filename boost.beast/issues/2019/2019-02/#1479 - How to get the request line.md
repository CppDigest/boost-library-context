# #1479 - How to get the request line [Closed]

> Username: ziwu14  
> Created at: 2019-02-26 14:01:49 UTC  
> Updated at: 2019-02-26 14:08:28 UTC  
> Closed at: 2019-02-26 14:08:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1479  

GET http://www.google.com/ HTTP/1.1^M  
Host: www.google.com^M  
User-Agent: curl/7.58.0^M  
Accept: */*^M  
Proxy-Connection: Keep-Alive^M  
  
I know how to use the iterator and [] operator to extract values for corresponding fields but if there is any member function that extract the request line"GET http://www.google.com/ HTTP/1.1"?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-02-26 14:03:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1479#issuecomment-467449742  

https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/target.html  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/method.html  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/method_string.html  
https://www.boost.org/doc/libs/1_69_0/libs/beast/doc/html/beast/ref/boost__beast__http__message/version.html
