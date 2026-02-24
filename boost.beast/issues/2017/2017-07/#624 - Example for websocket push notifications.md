# #624 - Example for websocket push notifications [Closed]

> Username: octopus-prime  
> Created at: 2017-07-08 17:25:00 UTC  
> Updated at: 2017-08-16 00:25:38 UTC  
> Closed at: 2017-08-16 00:25:38 UTC  
> Url: https://github.com/boostorg/beast/issues/624  

Would like to see an example demonstrating how to handle push notifications with websockets

---

## Comment 1

> Username: octopus-prime  
> Created at: 2017-07-08 17:27:25 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-313869577  

https://en.wikipedia.org/wiki/Push_technology

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-07-08 17:35:21 UTC  
> Updated at: 2017-07-08 17:36:02 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-313870036  

You handle it by always having a call to `async_read` posted, and when the server sends messages you'll receive them. No special behaviors are needed, websocket is a full duplex bidirectional protocol. You can send a message any time you want, and the remote peer can send you a message any time they want.  
  
`beast::websocket::stream` allows 1 asynchronous read and 1 asynchronous write to take place concurrently. You should always have a call to `stream::async_read` pending.

---

## Comment 3

> Username: octopus-prime  
> Created at: 2017-07-08 17:42:41 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-313870415  

> You handle it by always having a call to async_read posted  
  
So i have one callback for the async_read.  
How to distinguish between a response data and push data in only one callback?  
  
Here some code from nghttp2  
  
```  
    req->on_response([&sess](const response &res) {  
      std::cerr << "response received!" << std::endl;  
      res.on_data([&sess](const uint8_t *data, std::size_t len) {  
        std::cerr.write(reinterpret_cast<const char *>(data), len);  
        std::cerr << std::endl;  
      });  
    });  
  
    req->on_push([](const request &push) {  
      std::cerr << "push request received!" << std::endl;  
      push.on_response([](const response &res) {  
        std::cerr << "push response received!" << std::endl;  
        res.on_data([](const uint8_t *data, std::size_t len) {  
          std::cerr.write(reinterpret_cast<const char *>(data), len);  
          std::cerr << std::endl;  
        });  
      });  
    });  
  });  
```  
  
As you can see, there are two callbacks - for response and push.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-08 17:45:33 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-313870575  

```  
    req->on_response([&sess](const response &res) {  
...  
    req->on_push([](const request &push) {  
```  
  
I don't know nghttp2 very well at all, but it looks like the code you pasted is part of the HTTP/2 interfaces not the WebSocket:  
https://nghttp2.org/documentation/asio_http2_client.h.html

---

## Comment 5

> Username: octopus-prime  
> Created at: 2017-07-08 17:51:48 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-313870959  

Yes. Would like to see an example with Beast WebSocket that demonstrates exactly this behavior.  
  
1. Send a request.  
2. Wait for push and/or response.  
  
An example like "If you want to do something like push in HTTP/2 using Beast WebSockets you could do it like this: ..."

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-08 17:55:25 UTC  
> Updated at: 2017-07-08 17:56:47 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-313871205  

Its up to the application to choose a protocol or invent a protocol for associating "requests" and "responses" in WebSocket messages - the websocket protocol does not define "request" or "response". For example your application could use JSON-RPC for messages, and the JSON-RPC specification has an "id" field which associates message replies with their original request. And the JSON-RPC specification also prescribes how unsolicited messages are marked (without an id) - that's the "push". There can be ongoing responses associated with a particular request.  
  
My point is all this has nothing to do with Beast, which provides raw facilities to send and receive websocket messages. Its up to the application to decide when to send and what to do with them. You can "push" a message to the other end any time you want, by just calling `async_write`. And the remote peer can send messages to you any time they want. If you want some messages to be considered a "request" and other messages to be considered a "response" to that "request", then you need to define the contents of your message in a way that makes that association, for example by including a unique "ID" like JSON-RPC. Or using any other scheme.  
  
It is very unlikely that I am going to write an example for this because it would require a lot of code (in the client and the server), and I would have to either invent my own ad-hoc protocol (undesirable) or use a standard like JSON-RPC. which brings in a sizable dependency This is a huge example and one I feel should not be necessary.

---

## Comment 7

> Username: octopus-prime  
> Created at: 2017-07-08 18:05:50 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-313871880  

Okay. Got it.   
  
- Push is not specified for WebSockets.   
- WebSockets are bidirectional - that's all.  
  
So in WebSockets all is about parsing messages by self to get the required action (e.g. update cache or render view).

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-07-08 18:08:52 UTC  
> Updated at: 2017-07-08 18:09:06 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-313872077  

>So in WebSockets all is about parsing messages by self to get the required action (e.g. update cache or render view).  
  
That's right. Although, as I mentioned earlier, some protocols like JSON-RPC are naturally designed to work with bidirectional protocols with requests and responses, and push (subscription). For example, cryptocurrency exchange APIs allow you to subscribe to a stream of price quotes over websocket. Here's one of them:  
https://cex.io/websocket-api#encoding  
  
See **2.1 JSON-RPC over stream connections**  
http://json-rpc.org/wiki/specification  
  
See **4. Request Object**, note `id`  
http://www.jsonrpc.org/specification#request_object  
  
Thanks

---

## Comment 9

> Username: JuliusGel  
> Created at: 2017-07-20 11:38:59 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-316677686  

Hello, I have a question related to this issue. I have a custom WebSockets protocol. I implemented server that is exactly the same as the one in [WebSocket Server (Asynchronous)](https://github.com/vinniefalco/Beast/blob/master/example/websocket-server-async/websocket_server_async.cpp). I added a method to the connection class that is accessible from the outside and is called when the server wants to send status updates to connected clients. This method is called from the outside thread. Internally it has the following implementation:  
```c++  
void send_message(const std::string& message)  
{  
    ostream(send_buffer_) << message.c_str();  
    ws_.async_write(send_buffer_.data(),  
        strand_.wrap(std::bind(  
            &connection::on_write,  
            shared_from_this(),  
            std::placeholders::_1)));  
}  
```  
However, the documentation states that ws_ is not thread-safe, therefore I assume that calling `async_write` would be unsafe as well, even though handler is wrapped in strand? Would the correct method to implement this be to queue messages and send them in either `on_write` or `on_read`? Maybe you have some other suggestions? Thanks very much in advance.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-07-20 12:13:18 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-316684646  

> I assume that calling async_write would be unsafe as well, even though handler is wrapped in strand?  
  
That is correct.  
  
>Would the correct method to implement this be to queue messages and send them in either on_write or on_read?  
  
Yes. When a message finishes sending you can check the queue and if it is not empty then grab that element and start sending it. The design of the queue is up to you. But one problem arises, when you insert the first item to the queue (perhaps using a mutex) from a foreign thread, how do you get the connection to start sending it?  
  
The answer is to use `io_service::post` to execute a function on the strand:  
  
```  
auto const self = shared_from_this();  
ws_.get_io_service().post(strand_.wrap(  
    [self, &ws_]()  
    {  
        ws_.do_write({});  
    }));  
```  
  
Just put whatever you need into `do_write` for de-queuing an item and sending it. You can also use this technique to avoid the need for a mutex:  
  
```  
void send_message(const std::string& message)  
{  
    auto self = shared_from_this();  
    ws_.get_io_service().post(strand_.wrap(  
        [self, ws_, message]() mutable  
        {  
            put_queue(std::move(message));  
        }));  
}  
```  
  
This is just an example though, don't put the string in the lambda because Asio makes copies and that would be inefficient. You could put it in a `shared_ptr` or something.

---

## Comment 11

> Username: JuliusGel  
> Created at: 2017-07-20 12:33:51 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-316688975  

Thanks very much, this helped a lot.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2017-07-20 12:52:19 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-316693300  

Glad to hear it!

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-08-04 23:05:12 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-320371505  

Is this still an issue that requires attention?

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-08-16 00:25:37 UTC  
> Url: https://github.com/boostorg/beast/issues/624#issuecomment-322624789  

It looks like this is no longer an issue so I'm going to go ahead and close it. If it is still a problem feel free to re-open the issue or even better, create a new one - thanks!
