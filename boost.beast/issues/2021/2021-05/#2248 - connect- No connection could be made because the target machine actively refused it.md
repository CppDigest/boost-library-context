# #2248 - connect: No connection could be made because the target machine actively refused it [Closed]

> Username: ghost  
> Created at: 2021-05-28 09:10:35 UTC  
> Updated at: 2022-09-24 05:18:23 UTC  
> Closed at: 2022-09-24 05:18:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2248  

Hello to evrey one, i use vpn in my laptop, when i am connect to vpn and i try to sent request to server an get the response i got this message of error:  connect: No connection could be made because the target machine actively refused it. but when i am connect to wifi i recive the reponse from the server. i don t now why.

---

## Comment 1

> Username: TheStormN  
> Created at: 2021-06-03 12:19:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2248#issuecomment-853825488  

I think your issue is nothing related to Beast.

---

## Comment 2

> Username: ghost  
> Created at: 2021-06-03 13:21:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2248#issuecomment-853864018  

Now it s Beast issue, i shoud configure a proxy, but how i dont now

---

## Comment 3

> Username: TheStormN  
> Created at: 2021-06-03 13:36:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2248#issuecomment-853874791  

Beast does not directly support proxies, you will have to implement that yourself.

---

## Comment 4

> Username: ghost  
> Created at: 2021-06-03 13:58:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2248#issuecomment-853890955  

Ok,There is a methode to do our own message of exception, if i do i bad host,because i got message ```No such host is known``` what is the code error of that, i want to exlpain more that error using try and catch

---

## Comment 5

> Username: TheStormN  
> Created at: 2021-06-03 15:03:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2248#issuecomment-853939566  

By viewing your other opened issues I see that you are new to C++, networking, ASIO and Beast. You should start reading the docs, viewing the examples and in general start understanding how the whole machinery is working, before coming here asking generic questions. Perhaps StackOverflow would be a better place for your questions.  
  
If you want to skip some of that learning curve, you should go to a more high level libraries and frameworks. I would recommend the Qt Framework and the provided websocket there - https://doc.qt.io/qt-5/qtwebsockets-examples.html  
  
It does natively support proxies and other high level stuff which you are expecting.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2248#issuecomment-1008220756  

This issue has been open for a while with no activity, has it been resolved?
