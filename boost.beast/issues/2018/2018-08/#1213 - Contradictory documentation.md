# #1213 - Contradictory documentation [Closed]

> Username: reddwarf69  
> Created at: 2018-08-01 09:37:01 UTC  
> Updated at: 2019-02-10 13:51:57 UTC  
> Closed at: 2019-02-10 13:51:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1213  

In https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/notes.html it says `this code is well-formed`:  
  
```  
ws.async_read(b, [](error_code, std::size_t){});  
ws.async_write(b.data(), [](error_code, std::size_t){});  
ws.async_ping({}, [](error_code){});  
ws.async_close({}, [](error_code){});  
```  
  
In https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_close.html it says `The program must ensure that the stream performs no other write operations (such as websocket::stream::async_ping, websocket::stream::async_write, websocket::stream::async_write_some, or websocket::stream::async_close) until this operation completes. `  
  
Which seems a contradiction to me. Can `async_write` and `async_close` be pending at the same time or not?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-01 13:22:12 UTC  
> Updated at: 2018-08-01 13:22:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-409572489  

> Can async_write and async_close be pending at the same time or not?  
  
Yes. The documentation for `async_close` is wrong, sorry about that!  
  
The "Notes" are correct, a distinguishing feature of `websocket::stream` is that it supports all 4 of the asynchronous operations to be pending at the same time (quite difficult to implement but very convenient for users).

---

## Comment 2

> Username: reddwarf69  
> Created at: 2018-08-03 11:02:10 UTC  
> Updated at: 2018-08-03 11:06:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-410220163  

Just so I get it right. If I do this:  
  
```  
ws.async_read(b, [](error_code, std::size_t){});  
ws.async_write(b.data(), [](error_code, std::size_t){});  
ws.async_ping({}, [](error_code){});  
ws.async_close({}, [](error_code){});  
```  
  
What would happen is:  
- Maybe the async_read async op will complete, maybe it will not  
- Maybe the async_write async op will actually write something to the network, maybe it will not  
- Maybe the async_ping async op will actually write something to the network, maybe it will not  
- When `async_close` returns `is_open()` will be still true.  
  
Now... I suppose that, if they have not completed before, `async_read`, `async_write and `async_ping` will complete with "operation cancelled" whenever the websocket::stream stops being `is_open()`, right?  
  
Then, the async_close documentation says  
```  
The asynchronous operation will continue until one of the following conditions is true:  
    * The close frame finishes sending.   
```  
Does this mean the websocket::stream stops being `is_open()` when the `async_close()` async operation completes? Or it will stop being `is_open()` only when it receives a close frame back from the other endpoint? If it's the later, does this mean that the only way to close the Websocket connection correctly AND taking into account timeouts (never receiving the close frame back) is to:  
* set a `control_callback()`  
* set a timer when calling async_close()  
* close the lowest_layer() if the timer expires  
?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-08-03 11:09:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-410221705  

>  If it's the later, does this mean that...  
  
Yes. Your analysis is 100% correct. I bet you would write great docs :) These tests address the scenario you described:  
  
https://github.com/boostorg/beast/blob/develop/test/beast/websocket/close.cpp#L455  
  
I am working on a new class `timed_socket` which contains an `asio::basic_stream_socket` and installs a service which handles timeouts on reads automatically. If you would like to provide feedback on it, that would be great. It might address your use-case:  
  
https://github.com/vinniefalco/beast/blob/timed_stream/include/boost/beast/experimental/core/timed_socket.hpp#L25

---

## Comment 4

> Username: reddwarf69  
> Created at: 2018-08-03 11:36:42 UTC  
> Updated at: 2018-08-03 14:24:50 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-410227605  

If I got it right that would set a single read timeout for all the websocket::stream operations, right?  
  
In my use-case I am waiting for the other endpoint to send me instructions, not to reply to requests I send continuously. These instructions can come every 10 ms or once every 2 hours. I don't really want to timeout on async_read generically. I want to timeout only when waiting for a "pong" or a "close".  
  
On the other hand I could make use of that timed_socket as it is. I would just need to call `async_read()` again if it completes with a timed-out error and I know there have been no `ping` or `close` sent recently (would not be perfect, but...)  
  
On the yet other hand, it would not handle "bad" peers that could potentially ignore my close frame.  
* I async_close()  
* The other side ignores it and keeps sending me other messages  
* The socket never timeouts, even if I never receive the close frame, because the other messages keep resetting the timeout timer.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-08-03 14:34:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-410272762  

Hmm... you are right. Perhaps `basic_timed_socket` can expose additional interface to give you control over the timeout process:  
```  
template <class Protocol>  
class basic_timed_socket  
{  
    ...  
public:  
    ...  
    /** Arm the timer.  
        This function activates the timer. If the timer expires before  
        the member function `reset` is called, then on_timeout() will  
        be invoked.  
    */  
    void set();  
  
    /** Reset the timer.  
        If the timer is active, this will reset it, otherwise does nothing.  
    */  
    void reset();  
  
    /// Called when the timer expires  
    virtual void on_timeout() = 0;  
};  
```

---

## Comment 6

> Username: reddwarf69  
> Created at: 2018-08-03 15:52:47 UTC  
> Updated at: 2018-08-03 16:05:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-410296436  

How exactly would this look?  
  
```  
ws.async_close();  
ws.lowest_layer().set(std::chrono::seconds(10));  
  
...  
  
ws.control_callback(  
    [&ws](frame_type kind, string_view)  
    {  
	if(kind == frame_type::close) {  
	    ws.reset();  
	}  
    });  
  
...  
  
// Making it look like spawn without spawn for clarity purposes  
while(ws.is_open()) {  
    ws.async_read(..., handler_ec);  
    if(handler_ec == error::timedout) {  
        ws.lowest_layer().close();  
    }  
}  
```  
  
?  
  
It would certainly make it easier (the kind of easier that moves you from "fuck it, I will just close the tcp socket" to actually using it).  
  
You could argue something like this is easier to use  
  
```  
ws.async_close();  
ws.set_close_timeout(std::chrono::seconds(10));  
// ws.set_ping_timeout(std::chrono::seconds(10));  
  
...  
  
while(ws.is_open()) {  
    // Beast internally resets the timer only if a close frame arrives  
    ws.async_read(..., ec);  
    if(ec == error::timedout) { // "error::close_timeout"?  
        break;  
    }  
}  
```  
  
But it may go against the whole ASIO philosophy of getting out of the way letting the user implement it as he sees best. It looks like the easy vs flexible thing is difficult to get right, happy not to be the one who has to design these things :-p

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-08-03 22:51:12 UTC  
> Updated at: 2018-08-03 22:51:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-410395704  

Your proposed interface would certainly work, but it would require a separate timer per timed socket. But like you said it would be making a choice on behalf of the user. Getting the timer behavior right is very tricky. In fact the advanced-server and advanced-server-flex examples are wrong, and fixing them is non-trivial. This is not ideal and I think it needs to be simplified.  
  
I will open a new issue related to websocket timeouts, if you want to continue the conversation please do so there.

---

## Comment 8

> Username: Eelis  
> Created at: 2019-02-08 18:35:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-461901381  

To be clear: is the documentation still incorrect in Boost 1.69? Thanks

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-02-08 19:04:59 UTC  
> Updated at: 2019-02-08 19:06:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-461910669  

The Javadocs (the description above each function) are probably still wrong, e.g.  
https://github.com/boostorg/beast/blob/06efddd8b851610b5b3a5832ac87f1c52b838d9b/include/boost/beast/websocket/stream.hpp#L1758  
  
Fixing these docs is non-trivial...suggestions welcomed.  
  
Do you have a specific question that I might answer?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2019-02-08 23:07:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-461977839  

I'm working on this now, I'll see what I can do for 1.70 !

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-02-09 04:42:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-462013163  

Please have a look at this and let me know if it is sufficient or containers errors?  
https://github.com/vinniefalco/beast/commit/fd959990ed184f4d71950e798ffcd6533df65e48  
  
Thanks!

---

## Comment 12

> Username: vinniefalco  
> Created at: 2019-02-09 04:50:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-462013526  

@RedDwarf69 there is new information regarding timeouts in websocket streams:  
  
> I am working on a new class `timed_socket`  
  
This class is almost finished, but it is not appropriate for websocket for precisely the reasons that you specified. However, I am taking the lessons learned from writing this class and applying it to making the `websocket::stream` timeout enabled (with appropriately configurable user settings). It can automatically send the ping and handle the pong for you. Basically you set an option and forget about it, and everything Just Works. This is rather complicated but it will eventually arrive. I've been laying the foundations for it with some of the other changes (like `close_socket`, needed for timeouts).

---

## Comment 13

> Username: Eelis  
> Created at: 2019-02-09 08:37:39 UTC  
> Updated at: 2019-02-09 08:37:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-462025860  

Very nice!  
  
A minor nit: the Thread Safety notes talk about code that's "well-formed" and "malformed" presumably to suggest when UB may be expected, but the standard uses "well-formed" to [mean](http://eel.is/c++draft/defns.well.formed):  
  
> constructed according to the syntax rules, diagnosable semantic rules, and the one-definition rule

---

## Comment 14

> Username: vinniefalco  
> Created at: 2019-02-09 12:44:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-462041611  

> the Thread Safety notes talk about...  
  
I'm not seeing this, do you have a link? Here's the file:  
  
https://github.com/vinniefalco/beast/blob/v212/include/boost/beast/websocket/stream.hpp  
  
(or maybe you meant a different file?)

---

## Comment 15

> Username: Eelis  
> Created at: 2019-02-09 13:24:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1213#issuecomment-462044295  

I meant the Thread Safety notes at https://www.boost.org/doc/libs/1_67_0/libs/beast/doc/html/beast/using_websocket/notes.html from the opening post in this issue
