# #2958 - Channel timeout related issue [Closed]

> Username: shivank1111  
> Created at: 2024-12-02 12:02:24 UTC  
> Updated at: 2025-02-09 14:55:11 UTC  
> Closed at: 2025-02-09 14:55:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2958  

Boost Version: 354  
  
When opening two WebSocket channels, for example, `ws:/../channel1/` and `ws:/../channel2/`, I have made changes to enable communication between the two channels.  
  
I have set the timeout to 30 seconds using the following code:  
  
```cpp  
beast::websocket::stream_base::timeout opt{  
    std::chrono::seconds(30),  
    std::chrono::seconds(30),  
    false  
};  
ws_.set_option(opt);  
```  
  
  
  
However, the issue is that say both channels are opened at the same time. If, after 15 seconds, communication occurs between the two channels (e.g., from `channel1` to `channel2`), the timeout for `channel1` will restart and close the connection after 45 seconds(15+30 =45;from beginning). But for `channel2`, even though it is receiving messages, its timeout does not reset and will close exactly after 30 seconds, which I don't want.   
  
  
One possible solution is to keep the `alive_pings` set to `true` so the channel remains open, but I prefer not to use this approach, as it could increase the server load.  
Could you suggest another solution to handle this?   
  
Additionally, I have another question: If we implement a ping-pong mechanism, where the server receives a ping and responds with a pong to the client, would this reset the timeout? And how can I test this?

---

## Comment 1

> Username: ashtum  
> Created at: 2024-12-05 12:01:47 UTC  
> Url: https://github.com/boostorg/beast/issues/2958#issuecomment-2520126496  

> One possible solution is to keep the `alive_pings` set to `true` so the channel remains open, but I prefer not to use this approach, as it could increase the server load. Could you suggest another solution to handle this?  
  
Currently, only pong responses reset the timeout timer, so setting `alive_pings` to `true` is the only available option. There’s an open PR for this issue (https://github.com/boostorg/beast/pull/2718), but I haven’t merged it yet because the unit tests rely on a few timers, which makes them unreliable. I’ll work on resolving this and aim to merge it for the next Boost 1.88 release.

---

## Comment 2

> Username: shivank1111  
> Created at: 2024-12-05 12:23:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2958#issuecomment-2520175228  

My understanding with regard to pong responses is that if the client side sends a ping and we send a pong then the channel will remain open even if we set the alive_pings as false?

---

## Comment 3

> Username: ashtum  
> Created at: 2024-12-05 14:36:12 UTC  
> Updated at: 2024-12-05 14:36:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2958#issuecomment-2520497199  

> My understanding with regard to pong responses is that if the client side sends a ping and we send a pong then the channel will remain open even if we set the alive_pings as false?  
  
The internal inactivity timer is updated whenever the stream receives a message or a pong in response to a previously sent ping. `keep_alive_pings` controls whether the stream sends a ping message (when half of inactivity time is elapsed). It does not affect when the stream should be closed.

---

## Comment 4

> Username: shivank1111  
> Created at: 2025-01-10 09:29:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2958#issuecomment-2582170375  

Hi @ashtum , could you please confirm when the ping response feature, which is planned to be added in the Boost 1.88 release, will be available?

---

## Comment 5

> Username: ashtum  
> Created at: 2025-01-10 17:42:56 UTC  
> Updated at: 2025-01-10 17:44:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2958#issuecomment-2583339063  

> when the ping response feature, which is planned to be added in the Boost 1.88 release, will be available?  
  
Boost 1.88 will be released in April: https://www.boost.io/calendar/  
I'll try to fix the test problems in that PR and include it in the release.

---

## Comment 6

> Username: shivank1111  
> Created at: 2025-01-15 05:40:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2958#issuecomment-2591694760  

Hi @ashtum, Thanks for the update.
