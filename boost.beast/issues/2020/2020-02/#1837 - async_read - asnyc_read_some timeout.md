# #1837 - async_read / asnyc_read_some timeout [Closed]

> Username: jpramosi  
> Created at: 2020-02-05 11:14:53 UTC  
> Updated at: 2020-02-16 15:38:47 UTC  
> Closed at: 2020-02-16 15:38:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1837  

Hello my initial goal is to disconnect slow writing clients by timeout async_read on server.   
At first i looked into the beast's tcp_stream and websocket. There are already timers available for timeout async_read & async_write.  
However the most promising timer seems to be in tcp_stream, but i guess expires_after() doesn't fit well in my scenario, since for my needs i would need a timer to only start expire if i actually receive bytes from the client and not before (idle open connections).  
After i looked through the documentation again i found a promising solution for this.  
I could use async_read_some to read just a few bytes, start my custom timer and continue reading the whole message with async_read while being on async_read_some handler.  
  
Here's a short pseudo-code:  
  
```cpp  
class server_session  
{  
    ws_stream;  
    buffer;  
    timer;  
  
    server_session()  
    {  
        read();  
    }  
  
    void read()  
    {  
        // read just a few bytes  
        ws_stream.async_read_some(buffer, on_async_read_some);  
    }  
  
    void on_async_read_some()  
    {  
        // process the few buffer bytes..  
  
  
        // start timer  
        timer.expires_after(on_timer);  
        // read whole message  
        ws_stream.async_read(buffer, on_async_read);  
    }  
  
    void on_async_read()  
    {  
        // process whole message  
  
  
        // read next request  
        read();  
    }  
  
    void on_timer()  
    {  
        // cancel async_read on timeout  
        ws_stream.cancel();  
    }  
}  
```  
  
But my question is, would it work as expected or i need to change a few things? Does it actually continue reading this exact message which was sent by the client at first or it will awaiting the next message or doing something else i don't know?  
  
  
Version of Beast  
Version included with Boost 1.72.0

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-02-05 11:51:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1837#issuecomment-582372430  

Hi @reapler ,  
  
I think the only concern would be that `async_read_some` might actually read sufficient data to have read an entire frame. In which case it's probably prudent to place code similar to this in the function `on_async_read_some`:  
  
```  
    if (ws_stream.is_message_done())  
        on_async_read(ec, bytes_transferred); // assuming you have captured these arguments  
    else  
        ws_stream.async_read(buffer, on_async_read);  
```

---

## Comment 2

> Username: jpramosi  
> Created at: 2020-02-05 12:01:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1837#issuecomment-582375760  

Yea, that's a good idea.  
Thank you

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-02-05 15:39:54 UTC  
> Updated at: 2020-02-05 15:40:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1837#issuecomment-582467994  

You can't use the `tcp_stream` timers with `websocket::stream`
