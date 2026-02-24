# #2621 - Default value of the boost::beast::http::response result code. [Closed]

> Username: tkumpumaki  
> Created at: 2023-01-12 19:27:13 UTC  
> Updated at: 2023-10-28 16:34:23 UTC  
> Closed at: 2023-10-28 16:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2621  

I was debugging some bug caused by me, but while doing so I was mislead by the boost::beast::http::response return value, which stated it was 200 OK. Well it turned out the request did not even get to the result part, but it had that value by default.  
  
So my question is that, is there some reason to use 200 OK by default? For me it feels that it's possible to cause some confusion with that choice.  
  
Boost beast version 330

---

## Comment 1

> Username: ashtum  
> Created at: 2023-08-18 14:02:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2621#issuecomment-1683969412  

> So my question is that, is there some reason to use 200 OK by default? For me it feels that it's possible to cause some confusion with that choice.  
>   
  
@tkumpumaki It seems using any other status code would evoke the same question, and introducing an additional value to [http::status](https://www.boost.org/doc/libs/1_83_0/libs/beast/doc/html/beast/ref/boost__beast__http__status.html) (e.g. unspecified) would worsen the situation.
