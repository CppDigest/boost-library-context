# #1174 - exception occurred when running https example [Closed]

> Username: einiemand  
> Created at: 2018-07-01 06:39:28 UTC  
> Updated at: 2018-08-07 18:23:09 UTC  
> Closed at: 2018-08-07 18:23:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1174  

hello,  
  
I tried to run the http-client-async-ssl example but it said "cert already in hash table".   
![qq 20180701143627](https://user-images.githubusercontent.com/34703053/42131762-69368e74-7d3c-11e8-8baf-9dc919ad98d6.png)  
![qq 20180701143646](https://user-images.githubusercontent.com/34703053/42131763-698f83d0-7d3c-11e8-8a84-6c3b886c37b8.png)  
![qq 20180701143718](https://user-images.githubusercontent.com/34703053/42131764-69e75f7e-7d3c-11e8-9808-70809821ce29.png)  
  
it seems the exception is thrown by load_root_certificates

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-07-01 15:19:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1174#issuecomment-401613322  

Which version of the examples and which version of Beast?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-07-01 16:00:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1174#issuecomment-401615822  

I tried the example from the **master** branch and it works for me.

---

## Comment 3

> Username: einiemand  
> Created at: 2018-07-01 17:33:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1174#issuecomment-401621100  

I copied code from https://www.boost.org/doc/libs/1_67_0/libs/beast/example/http/client/async-ssl/http_client_async_ssl.cpp, and boost version is 1.67.0, openssl version is 1.1.0.  
  
thx for your reply!

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-07-31 17:37:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1174#issuecomment-409305419  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2018-08-07 18:23:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1174#issuecomment-411154159  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
