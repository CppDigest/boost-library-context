# #2937 - Boost::Beast websocket does not send pong [Closed]

> Username: danilsh  
> Created at: 2024-10-07 11:26:58 UTC  
> Updated at: 2024-10-16 18:38:15 UTC  
> Closed at: 2024-10-16 18:38:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2937  

I'm developing a websocket clent listening for Binance market data stream to read a quotes. I'm using Boost::Beast `websocket` for this purpose. The connection uses SSL. My problem is that my client disconnecting because of "pong timeout".  
  
Beast documentation says that websocket should automatically handle control frames and send pong with ongoing read or write operations. Because my websocket stream listening for quotes, it is almost all the time in permanent `async_read` state - so it either awaits data or reads data. So according to docs it should send pong when ping arrived.  
  
To check whether client receives pings I override `control_callback` - yes it receives pings. I even tried to send pongs from control callback using `async_pong`. This method's completion handler repots me that a pong send was successfull, but nothing changes.  
  
To check pong receipt on server side (Binance), I compiled an example of websocket ssl async server from Boost::Beast. I slightly changed it's code so it not echo received requests but rather statrt to send a json with quotes like Binance do. And I override `control_callback` on server side to see if pongs are coming from my client. They are **not**.  
  
My question is - why websocket not sending pongs?

---

## Comment 1

> Username: ashtum  
> Created at: 2024-10-07 12:20:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2396776385  

> Beast documentation says that websocket should automatically handle control frames and send pong with ongoing read or write operations.  
  
Correct, but only the `async_read` operation responds to ping messages. This means you need to have an active `async_read` operation if you want to respond in time.  
  
> To check whether client receives pings I override control_callback - yes it receives pings. I even tried to send pongs from control callback using async_pong  
  
No need to call `async_pong` inside that callback; it should send the pong automatically.  
  
> to see if pongs are coming from my client. They are not  
  
Have you tried using one of the existing client examples to see if pongs are coming from your client?

---

## Comment 2

> Username: danilsh  
> Created at: 2024-10-07 16:56:17 UTC  
> Updated at: 2024-10-07 17:53:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2397440245  

My client actually based on Beast async ssl websocket client example. The only siginficant changes is that my client uses `executor_work_guard` to prevent `io_context` from terminating it's `run()` method if no work to do. And my client do a constant calls to `async_read` instead of just do one read and terminate. All other parts are exactly the same: SSL initialization, resolve, connection, ssl and websoclet handshakes, timeout settings etc...

---

## Comment 3

> Username: ashtum  
> Created at: 2024-10-07 17:52:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2397543829  

> My client actually based on Beast async ssl websocket client example. The only siginficant changes is that my client uses `executor_work_guard` to prevent `io_context` from terminating it's `run()` method if no work to do. And my client do a constant calls to `async_read` instead of just do one read and terminate. All other parts are exactly the same: SSL initialization, resolve, connection, ssl and websoclet handshakes, timeout settings etc...  
  
There isn't enough data to assist. Could you please provide more information on the issue or attempt further debugging? I suggest starting with one of the client examples and modifying it enough to receive a simple tick or something, then observe how it behaves in response to a ping.

---

## Comment 4

> Username: danilsh  
> Created at: 2024-10-07 18:15:38 UTC  
> Updated at: 2024-10-07 19:48:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2397584917  

What do you suggest? Should I implement a client\server pair based on Beast examples and reproduce the behavior I described? Or?... What and where should I debug? Or you want to do it by yourself? It would be great If you tell me how else I could monitor my client receives ping and sends pong. The only mean I know is to redefine `control_callback`, or do a "step-into" debug.

---

## Comment 5

> Username: IYanakiev34  
> Created at: 2024-10-07 18:37:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2397624079  

I work on exchange connectivity. It is good practice to send ping frames but in most exchanges you have to send dedicated ping message over the websocket to the proper channel to keep the connection alive. Do not rely solely on ping and pong frames over the websocket layer. You will get lots of disconnects over a long running program.

---

## Comment 6

> Username: danilsh  
> Created at: 2024-10-07 19:48:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2397755246  

@IYanakiev34 here I have to obey Binance requirements for websocket client. I can not organize my own ping sequence. Binance server sends pings to my client and my client should respond with pongs. Here Binance follows RFC 6455 without any additions if I get it right.

---

## Comment 7

> Username: ashtum  
> Created at: 2024-10-07 20:44:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2397859057  

> What do you suggest? Should I implement a client\server pair based on Beast examples and reproduce the behavior I described? Or?... What and where should I debug? Or you want to do it by yourself? It would be great If you tell me how else I could monitor my client receives ping and sends pong. The only mean I know is to redefine `control_callback`, or do a "step-into" debug.  
  
I've updated `websocket_client_async_ssl.cpp` to create a read loop, and it maintains a stable connection with `wss://ws-api.binance.com:443/ws-api/v3`.  
Code: https://godbolt.org/z/MnE79fdf9  
Run it like:  
```C++  
./websocket-client-async-ssl ws-api.binance.com 443 "{\"id\": \"922bcc6e-9de8-440d-9e84-7c80933a8d0d\",\"method\": \"ping\"}"  
```

---

## Comment 8

> Username: danilsh  
> Created at: 2024-10-08 15:00:24 UTC  
> Updated at: 2024-10-08 15:12:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2400097694  

@ashtum I had slightly changed your example so it reflects my work environment:  
* I had changed endpoint to /stream, because I need this endpoint for my client  
* I had changed a server to stream.binance.com:9443, because it is used in Binance docs https://binance-docs.github.io/apidocs/spot/en/#websocket-market-streams  
* I had added a buffer reset code `buffer_ = {}` in completion handlers `onWrite` and `onRead`  
Here is a code: https://godbolt.org/z/MYxj8KdPc  
  
please, run it like this;  
  
`./websocket-client-async-ssl stream.binance.com 9443 "{\"id\": \"9\",\"method\": \"SUBSCRIBE\", \"params\": [ \"btcusdt@bookTicker\" ] }"`  
  
I have a disconnection with message:  
  
`0 : Pong timeout  
read: stream truncated`  
  
Maybe it's important to note that I'm running code on Windows 10, not Linux. And I'm using Boost 1.85  
  
UPD:  a disconnection period is fluctuating and I think to be sure better check it within 30min - 1hr. But according to Binance docs, session should live for 24hr

---

## Comment 9

> Username: danilsh  
> Created at: 2024-10-15 17:39:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2414635258  

@ashtum UPD:   
  
I tried also /ws endpoint with the same results - pong timeout and stream truncated. The method I used was not a ping but SUBSCRIBE, because I need to receive a stream of quotes.  
  
I need a working solution, so I'm trying to switch to Oat++ library now, but I'm still interested in solving this issue for a future.

---

## Comment 10

> Username: danilsh  
> Created at: 2024-10-16 18:38:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2937#issuecomment-2417620977  

The problem was solved. This is ridiculous but corporate antivirus has blocked websocket control frames.  
  
Thanks to all who tried to help
