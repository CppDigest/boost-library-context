# #360 - newbie question on thread safety and strands [Closed]

> Username: vchang-akamai  
> Created at: 2017-05-12 23:21:00 UTC  
> Updated at: 2017-05-16 22:16:51 UTC  
> Closed at: 2017-05-16 22:16:51 UTC  
> Url: https://github.com/boostorg/beast/issues/360  

The thread safety page states "The asynchronous interface supports one active read and one active write simultaneously. "  
[http://vinniefalco.github.io/beast/beast/websocket/threads.html](url)  
  
I've been wrapping all my async_* calls for a websocket with a single explicit strand associated with that websocket.  Does the above statement mean that I should be using two strands per websocket: one for reads and one for writes?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-05-12 23:42:55 UTC  
> Updated at: 2017-05-12 23:43:43 UTC  
> Url: https://github.com/boostorg/beast/issues/360#issuecomment-301209048  

No. The strand just means protects the stream so that two completion handlers do not execute concurrently. You still need to make sure that you don't do two reads or writes at once, without an intermediate completion handler invocation. For example, this code produces undefined behavior:  
```  
template<class NextLayer>  
void undefined(beast::websocket::stream<NextLayer>& ws);  
{  
  beast::multi_buffer b1;  
  beast::websocket::opcode op1;  
  ws.async_read(op1, b1,  
    []()  
    {  
    });  
  beast::multi_buffer b2;  
  beast::websocket::opcode op2;  
  ws.async_read(op2, b2,  
    []()  
    {  
    });  
```  
  
There cannot be more than one active read and one active write at the same time.

---

## Comment 2

> Username: vchang-akamai  
> Created at: 2017-05-16 22:16:43 UTC  
> Url: https://github.com/boostorg/beast/issues/360#issuecomment-301931570  

Thanks for the clarification! I have some things to fix... :-)
