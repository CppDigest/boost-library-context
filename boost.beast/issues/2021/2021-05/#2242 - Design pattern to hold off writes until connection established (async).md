# #2242 - Design pattern to hold off writes until connection established (async) [Closed]

> Username: Tectu  
> Created at: 2021-05-25 11:14:50 UTC  
> Updated at: 2021-06-07 10:24:17 UTC  
> Closed at: 2021-05-26 11:05:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2242  

I'm currently in the process of writing a WebSocket client built on top of beast using an asynchronous client session.  
The various examples mainly send a string to the WebSocket server and wait for the echo-response.  
For more general use, I added a `void write(const std::string& payload)` function to the `session` class to allow the user to send arbitrary data over the WebSocket connection.  
  
I'm wonder what the recommended design pattern is for ensuring that writes are not actually triggering `on_write()` until the connection is ready.  
Given the following scenario:  
```cpp  
auto conn = std::make_shared<session>();  
conn->run("127.0.0.1", "8080", "/end/point");  
conn->write("Hello WebSocket!");  
```  
The `write()` function will be executed before the connection is up and ready - due to the asynchronous implementation of the session.  
Note that the `write()` function can optionally also just write to a queue and return immediately - but the problem remains: How to ensure that `async_write` does not get called until the connection is ready and once it is - writing the current contents of the write queue?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-05-25 11:37:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-847794865  

You would use a write queue.   
  
e.g.  
  
https://github.com/test-scenarios/boost_beast_websocket_echo/blob/a4dda70ef563f78dca2035e44ba94a38be2295dc/pre-cxx20/echo_server/connection.cpp#L123

---

## Comment 2

> Username: Tectu  
> Created at: 2021-05-25 15:39:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-847979655  

Just wondering, is [this](https://github.com/test-scenarios/boost_beast_websocket_echo/tree/a4dda70ef563f78dca2035e44ba94a38be2295dc/cxx20) a complete, fully working example of creating a websocket connection using C++20 coroutines? Can one adopt from that or would you discourage that?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-05-25 16:47:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-848042856  

Yes it’s complete. I wrote it as an example

---

## Comment 4

> Username: Tectu  
> Created at: 2021-05-25 21:37:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-848283514  

A question on [this sample](https://github.com/test-scenarios/boost_beast_websocket_echo/blob/a4dda70ef563f78dca2035e44ba94a38be2295dc/pre-cxx20/echo_server/connection.cpp#L123) that you provided: In `send()`, why are you issuing an asynchronous function there? One might put the contents of `handle_send()` directly in `send()`, right? The `handle_send()` function seems relatively small and non-blocking by nature.

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-05-26 10:19:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-848651730  

:-) I do this for two reasons:  
  
1. I have a convention with async implementation classes - methods that are worded like orders are public functions, so handle the marshalling to the correct executor. Methods that implement what was marshalled are named `handle_xxx` and made private. In this way it's obvious (to me) what the rules of calling each function are: if it begins with `handle_...` then you'd better be calling from the correct strand (in fact I often assert that you are).  
2. In this way, other parts of the private implementation can avoid the call to `dispatch` if they know they are already on the correct executor, as they can call the `handle_xxx` form directly.  
  
Once the code is compiled in a release build, I strongly suspect that the redundant function call would be elided so I consider is a zero cost abstraction.

---

## Comment 6

> Username: Tectu  
> Created at: 2021-05-26 11:05:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-848677390  

Gotcha - Thanks for your explanation!  
  
I also managed to get the tx queue implemented so things appear to be working properly now. I still need a (decent) way of stresstesting this system but I guess that will be an _issue_ for another day ;-)

---

## Comment 7

> Username: ddevienne  
> Created at: 2021-05-26 12:45:51 UTC  
> Updated at: 2021-05-26 12:46:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-848739200  

> called from the correct strand (in fact I often assert that you are)  
  
Hi Richard. How does one assert a method is called from the correct strand? I'd really like to do that in my own code too.

---

## Comment 8

> Username: Tectu  
> Created at: 2021-05-27 22:09:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-849974451  

@madmongo1 ^

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-05-28 02:41:53 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-850069745  

io_context::executor_type::running_in_this_thread()  
  
https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/io_context__executor_type/running_in_this_thread.html

---

## Comment 10

> Username: ddevienne  
> Created at: 2021-06-07 09:51:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-855784683  

Thanks @madmongo1. Very useful.  
  
Initially I was confused, then realized a strand was an _executor_type_ I guess, and found  
https://www.boost.org/doc/libs/1_73_0/doc/html/boost_asio/reference/io_context__strand/running_in_this_thread.html

---

## Comment 11

> Username: madmongo1  
> Created at: 2021-06-07 10:24:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2242#issuecomment-855807009  

Great!
