# #1001 - WebSocket ZMQ gateway architecture recommendations [Closed]

> Username: Getafix-hub  
> Created at: 2018-01-30 14:50:16 UTC  
> Updated at: 2018-02-15 22:31:53 UTC  
> Closed at: 2018-02-15 22:31:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1001  

Hi,   
  
First of all, kudos to @vinniefalco, boost/beast is quite impressive!   
  
I am looking for recommendations to implement a WebSocket ZMQ gateway using boost/beast.  
For starting point, I used [boost/beast 1.66 websocket example](http://www.boost.org/doc/libs/1_66_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp)   
  
I plan to augment the demo with a zmq socket listener and forward incoming messages to the websocket.     
I am contemplating the following options  
- integrate zmq using boost asio library    
- leverage [azmq](https://github.com/zeromq/azmq)  
  
Any recommendations?   
  
Thanks in advance.

---

## Comment 1

> Username: rodgert  
> Created at: 2018-01-30 17:49:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1001#issuecomment-361676323  

I can't comment on the specifics of Beast, but, as the author of azmq, I  
don't know of any reason why it shouldn't work, and I would be happy to  
help make it work for this use case.  
  
I have separate, longer term project going on to re-implement zmq's framing  
protocol and basic handling for zmq sockets directly on Asio, but it's far  
from ready for use.  
  
On Tue, Jan 30, 2018 at 8:50 AM, Chris <notifications@github.com> wrote:  
  
> Hi,  
>  
> First of all, kudos to @vinniefalco <https://github.com/vinniefalco>,  
> boost/beast is quite impressive!  
>  
> I am looking for recommendations to implement a WebSocket ZMQ gateway  
> using boost/beast.  
> For starting point, I used boost/beast 1.66 websocket example  
> <http://www.boost.org/doc/libs/1_66_0/libs/beast/example/websocket/server/async/websocket_server_async.cpp>  
>  
> I plan to augment the demo with a zmq socket listener and forward incoming  
> messages to the websocket.  
> I am contemplating the following options  
>  
>    - integrate zmq using boost asio library  
>    - leverage azmq <https://github.com/zeromq/azmq>  
>  
> Any recommendations?  
>  
> Thanks in advance.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/beast/issues/1001>, or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AAHYB5u8Tsjdynru1PWjh2ZQYjizDIuRks5tPywpgaJpZM4Ryc0o>  
> .  
>

---

## Comment 2

> Username: Getafix-hub  
> Created at: 2018-01-31 03:24:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1001#issuecomment-361811917  

Thank you @rodgert, much appreciated.   
Stay tuned.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-02-15 20:06:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1001#issuecomment-366045869  

Has this issue been resolved?

---

## Comment 4

> Username: Getafix-hub  
> Created at: 2018-02-15 22:31:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1001#issuecomment-366083900  

Yes. I don't have the specifics at the task has been re-assigned but we were able to integrate ZMQ with Beast
