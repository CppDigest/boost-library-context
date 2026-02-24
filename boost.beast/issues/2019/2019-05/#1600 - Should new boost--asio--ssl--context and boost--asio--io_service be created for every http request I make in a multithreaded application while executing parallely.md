# #1600 - Should new boost::asio::ssl::context and boost::asio::io_service be created for every http request I make in a multithreaded application while executing parallely? [Closed]

> Username: opengpu  
> Created at: 2019-05-04 07:25:27 UTC  
> Updated at: 2019-06-15 14:37:02 UTC  
> Closed at: 2019-06-15 14:37:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1600  

Should new boost::asio::io_context be created for every http request?  
what is the best way to deal with io_context & call io_conxt.run()

---

## Comment 1

> Username: stenreijers  
> Created at: 2019-05-04 11:32:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1600#issuecomment-489318881  

By default the io_context stops running when there are no more tasks queued to be executed after the run() call. Depending on your application design you can keep the io_context alive even when there is no more work by using boost::asio::executor_work_guard. You can keep posting work to the context and it gets executed.  
  
Example  
```   
boost::asio::io_context ioc;  
boost::asio::executor_work_guard<boost::asio::io_context::executor_type> guard(boost::asio::make_work_guard(ioc));  
  
// Runs the context  
ioc.run();  
  
// You can keep posting work here  
```

---

## Comment 2

> Username: djarek  
> Created at: 2019-05-04 11:32:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1600#issuecomment-489318918  

It's best to reuse contexts, because they may contain state that's fairly expensive to construct (and destroy).

---

## Comment 3

> Username: opengpu  
> Created at: 2019-05-04 15:04:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1600#issuecomment-489334765  

thank you.  
but eg. @async http demo,   
how to reuse it...  
as in Client, user log in, and then do nothing. except when user click button, a http msg is sent to Server.  
it seems hard to reuse, bcause http is short connection and will close each time message is sent.

---

## Comment 4

> Username: jpramosi  
> Created at: 2019-05-06 10:27:11 UTC  
> Updated at: 2019-05-09 13:39:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1600#issuecomment-489574600  

I use myself a solution to async_read in a loop, so it will listen to the server & keep the client alive (also useful for a broadcast facility). However i use websocket. ~, but it should also work with http.~  
To send something on demand to the server from another thread, you could use [post](https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/reference/post.html) to queue work in the executor in order to async_write a message.  
A complete example can be found [here](https://www.boost.org/doc/libs/1_70_0/libs/beast/example/websocket/server/chat-multi/).

---

## Comment 5

> Username: opengpu  
> Created at: 2019-05-07 07:58:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1600#issuecomment-489976187  

@reapler thanks. what situation is the best senario to use websocket?

---

## Comment 6

> Username: jpramosi  
> Created at: 2019-05-09 13:28:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1600#issuecomment-490904472  

Well, the Websocket protocol offers a full-duplex communication. This means the client AND the server can receive & send messages after the established Websocket handshake.  
This is useful for example if you are going to broadcast a message to clients.  
I can imagine that it is useful for chat or game servers.  
For a better consideration between Http, Websocket or tcp socket i would view these [answers](https://stackoverflow.com/questions/14703627/websockets-protocol-vs-http).

---

## Comment 7

> Username: stale[bot]  
> Created at: 2019-06-08 14:32:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1600#issuecomment-500128667  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: stale[bot]  
> Created at: 2019-06-15 14:37:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1600#issuecomment-502371992  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
