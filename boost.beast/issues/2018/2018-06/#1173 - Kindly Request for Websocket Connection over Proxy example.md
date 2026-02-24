# #1173 - Kindly Request for Websocket Connection over Proxy example [Closed]

> Username: abeydemir23  
> Created at: 2018-06-29 07:42:24 UTC  
> Updated at: 2018-08-09 15:12:48 UTC  
> Closed at: 2018-08-09 15:12:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1173  

Hi, I look inside the beast library and it's examples about Websocket connection over a proxy but no chance up to the present my actual intention could be explanied using below image please have a look at.  
  
https://user-images.githubusercontent.com/12622800/42079517-94912a3a-7b88-11e8-987f-879939874c43.png  
  
Since I'm a newbie in both C++ and boost/beast i kindly request a websocket client example over proxy.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-06-29 13:00:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1173#issuecomment-401347044  

> I'm a newbie in both C++ and boost/beast  
  
You are going to have a very difficult time using Beast successfully without a grasp of C++ and Boost.Asio. I suggest you take a step back and first become proficient in C++, and then learn Boost.Asio. Then the Beast documentation will be more approachable, and you will be able to understand the examples.

---

## Comment 2

> Username: abeydemir23  
> Created at: 2018-07-02 06:09:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1173#issuecomment-401681758  

Hi @vinniefalco actually I've managed to compile and run examples on repository My only need is proxy enhancement or any code change set that provide proxy support.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-07-02 13:21:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1173#issuecomment-401802193  

Okay, well I suggest you read the documentation or specification on the type of proxy support you need, and then add the necessary code.

---

## Comment 4

> Username: abeydemir23  
> Created at: 2018-07-03 05:53:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1173#issuecomment-402021271  

Hi @vinniefalco i read the documentation which is below https://www.boost.org/doc/libs/develop/libs/beast/doc/html/index.html   
and found out that proxy support seems not supported by beast  
  
About specification of proxy type it can be any outgoing explicit HTTP proxy for regular and secure websockets.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-07-03 14:14:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1173#issuecomment-402172360  

If you want to work with a proxy, you have to write that yourself. Beast only implements the low-level HTTP and WebSocket protocols.

---

## Comment 6

> Username: stale[bot]  
> Created at: 2018-08-02 15:07:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1173#issuecomment-409960163  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 7

> Username: stale[bot]  
> Created at: 2018-08-09 15:12:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1173#issuecomment-411792825  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
