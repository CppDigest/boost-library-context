# #2569 - [doc] not the coroutines that I was expecting for [Closed]

> Username: madhur4127  
> Created at: 2022-11-28 12:39:49 UTC  
> Updated at: 2022-11-28 12:50:06 UTC  
> Closed at: 2022-11-28 12:48:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2569  

I would kinda expect `Websocket, coroutine` to be a C++20 coroutine as its part of the language now. Its a bummer that its a stackful boost coroutine example.  
  
Maybe we can :  
1. `Websocket, coroutine` -> `Websocket, stackful boost coroutine`  
2. Change `Websocket, coroutine` example code to have C++20 coroutine.  
  
https://www.boost.org/doc/libs/1_80_0/libs/beast/doc/html/index.html  
  
![image](https://user-images.githubusercontent.com/29328609/204280055-7a9a1839-2269-448b-b342-9be07282f33e.png)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-11-28 12:48:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2569#issuecomment-1329030369  

Coming in 1.81: https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/examples.html  
  
Note that ther eis no C++20 coroutine type, the examples ues `asio::awaitable` which is one possible implementation to use.

---

## Comment 2

> Username: madhur4127  
> Created at: 2022-11-28 12:50:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2569#issuecomment-1329033255  

thanks
