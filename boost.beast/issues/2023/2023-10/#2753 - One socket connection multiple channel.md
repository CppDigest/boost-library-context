# #2753 - One socket connection multiple channel [Closed]

> Username: ksohan  
> Created at: 2023-10-20 06:13:13 UTC  
> Updated at: 2023-10-20 11:18:37 UTC  
> Closed at: 2023-10-20 11:18:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2753  

Is it possible to create multiple channel in one socket connection so that different request's responses are sent over different channels of the connection. If yes, is there any example of how to do that?

---

## Comment 1

> Username: ashtum  
> Created at: 2023-10-20 08:09:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2753#issuecomment-1772277162  

> Is it possible to create multiple channel in one socket connection so that different request's responses are sent over different channels of the connection. If yes, is there any example of how to do that?  
  
Could you please explain what you mean by `multiple channels in one socket`? are you referring to `asio::experimental::channel` ?

---

## Comment 2

> Username: ksohan  
> Created at: 2023-10-20 08:29:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2753#issuecomment-1772305227  

I am actually not much familiar `asio::experimental::channel`. So let me explain the scenario, After the client is connected to the server, it subscribes for data. I have two types of data in JSON format which needs to be sent continuously upon subscription.  
1. Data1: {"x": 11, "y": 12}  
2. Data2: {"x": 100, "z" : 35}  
  
Now these two types of data are being sent over the socket connection and the client has no way to distinguish which data type is the current received data. That's why I was asking about channel so that I can send data1 in one channel and data2 in another channel.

---

## Comment 3

> Username: fpelliccioni  
> Created at: 2023-10-20 08:52:56 UTC  
> Updated at: 2023-10-20 08:53:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2753#issuecomment-1772339809  

@ksohan Before diving into potential solutions, could you please confirm if you're using TCP sockets or WebSockets for your communication?  
  
As far as I know, in TCP sockets, there's no built-in concept of "channels". Given that you're working with JSON, typically you would need to distinguish between the data types at a higher layer where you parse the JSON.  
  
Once you confirm the type of socket you're using, we can discuss more detailed solutions tailored to your setup.

---

## Comment 4

> Username: ksohan  
> Created at: 2023-10-20 09:05:33 UTC  
> Updated at: 2023-10-20 10:00:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2753#issuecomment-1772358408  

I am using [this](https://github.com/boostorg/beast/tree/develop/example/websocket/server/chat-multi) WebSocket example.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2023-10-20 10:58:53 UTC  
> Updated at: 2023-10-20 10:59:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2753#issuecomment-1772529500  

Add another key/value pair to identify the entity:  
```  
Data1: { "x":  11, "y": 12, "id" : 1 }  
Data2: { "x": 100, "z" : 35, "id" : 2 }  
```
