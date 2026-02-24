# #2802 - multiplexing [Closed]

> Username: djn3m0  
> Created at: 2024-01-19 10:55:43 UTC  
> Updated at: 2024-01-22 11:16:12 UTC  
> Closed at: 2024-01-22 11:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2802  

Hi,  
  
I'm trying to manage different connections through one connection. I haven't seen any clear example or document on how to do so. Does Boost Beast support multiplexing?  
  
If yes please provide a clue on how I can achieve it, if not, please tell me if you have any idea how I can achieve this.  
  
Thanks

---

## Comment 1

> Username: ashtum  
> Created at: 2024-01-19 11:35:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2802#issuecomment-1900247039  

Do you mean multiplexing in WebSocket? If so, there is no standardized method for multiplexing messages within a single stream. This task is considered a higher-level responsibility for the user. For instance, if you are working with JSON messages, you might structure them as follows:  
 ```JSON  
{  
  "stream_id": "1",  
   "payload": "..."  
}  
```

---

## Comment 2

> Username: djn3m0  
> Created at: 2024-01-19 12:32:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2802#issuecomment-1900334885  

Do you know any project that has implemented this?

---

## Comment 3

> Username: ashtum  
> Created at: 2024-01-19 13:18:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2802#issuecomment-1900413368  

> Do you know any project that has implemented this?  
  
Not that I'm aware of. but it shouldn't be difficult to implement. what is the message format that you are using?

---

## Comment 4

> Username: djn3m0  
> Created at: 2024-01-19 13:22:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2802#issuecomment-1900418531  

I'm trying to write a WebSocket tunnel that can accept multiple TCP connection and send them through WebSocket.

---

## Comment 5

> Username: ashtum  
> Created at: 2024-01-22 06:53:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2802#issuecomment-1903367549  

Your message format should be in binary. A straightforward method of multiplexing involves appending a single byte to the start of each message, indicating the stream's state and ID. You may find the followings useful: https://ckousik.github.io/gsoc/2017/06/16/WebSocket-Multiplexer-Overview.html  
https://github.com/xtaci/smux

---

## Comment 6

> Username: djn3m0  
> Created at: 2024-01-22 11:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2802#issuecomment-1903787955  

Cool thanks.
