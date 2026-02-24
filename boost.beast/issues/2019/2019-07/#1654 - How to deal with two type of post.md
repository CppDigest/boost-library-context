# #1654 - How to deal with two type of post [Closed]

> Username: brokensword2018  
> Created at: 2019-07-10 04:23:39 UTC  
> Updated at: 2019-08-16 06:47:52 UTC  
> Closed at: 2019-08-16 06:47:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1654  

I want to write a server with beast,  sometimes the request is http::request<http::string_body>, sometimes is http::request<http::file_body>. the example of server show that the request  can only be defined one type.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-10 04:25:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1654#issuecomment-509902093  

Yes: https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/more_examples/change_body_type.html

---

## Comment 2

> Username: brokensword2018  
> Created at: 2019-07-10 06:31:33 UTC  
> Url: https://github.com/boostorg/beast/issues/1654#issuecomment-509928243  

> Yes: https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/more_examples/change_body_type.html  
  
thanks a lot.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-08-09 06:47:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1654#issuecomment-519800836  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-08-16 06:47:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1654#issuecomment-521905463  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
