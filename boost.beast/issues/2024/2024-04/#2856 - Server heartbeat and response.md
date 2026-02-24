# #2856 - [HELP] Server heartbeat and response [Closed]

> Username: zptan  
> Created at: 2024-04-22 04:01:04 UTC  
> Updated at: 2024-04-25 01:05:06 UTC  
> Closed at: 2024-04-25 01:05:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2856  

I am asking for help, just came across a scenario:  
1. Websocket client can send requests at any time  
2. Websocket server sends heartbeat message (payload, not a control frame) to the client every 15s  
  
For 2, it is not a control frame (like ping) so we have to drain the heartbeat messages explicitly, or the heartbeat messages will pile up. We can use a steady_timer.  
  
The difficulty is: we may read the app response when trying to read a heartbeat message:  
1. The job to read heartbeat message has been scheduled, the handler (let's call it hb_handler) has been put to the handler queue  
2. Currently there is no heartbeat message sent from server  
3. A request sent, the handler (let's call it resp_handler) has been put to the handler queue  
4. When the response returns, hb_handler handles it, resp_handler gets nothing, that is not what we want!  
  
I am not able to figure out an elegant solution, can anyone help?

---

## Comment 1

> Username: ashtum  
> Created at: 2024-04-22 12:22:44 UTC  
> Updated at: 2024-04-22 12:24:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2856#issuecomment-2069259668  

Based on your description, it appears that you are calling `async_read` only when you anticipate receiving a message (resulting in unexpected ping messages queuing up). Typically you should call `async_read` within an asynchronous loop so that you can drain all received messages.  
  
If you can't change your design I would suggest to add that `async_read` loop that filters out those heartbeat messages and adds the received message to a [experimental::basic_channel](https://www.boost.org/doc/libs/1_85_0/doc/html/boost_asio/reference/experimental__basic_channel.html) this way you can still wait for new messages asynchronously (on the channel instead of the websocket stream)

---

## Comment 2

> Username: zptan  
> Created at: 2024-04-22 13:36:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2856#issuecomment-2069462321  

@ashtum Thanks for you suggestion, we will try.
