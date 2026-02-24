# #776 - Buffer with uint8_t type invalid encoding [Closed]

> Username: Voultapher  
> Created at: 2017-09-10 16:13:41 UTC  
> Updated at: 2017-09-21 16:40:20 UTC  
> Closed at: 2017-09-21 16:40:20 UTC  
> Url: https://github.com/boostorg/beast/issues/776  

### Version of Beast  
  
Latest develop  
  
### Steps necessary to reproduce the problem  
```cpp  
void sendResponse(websocket::stream<tcp::socket>& ws)  
 {  
  std::array<uint8_t, 1> err_buff{{ static_cast<uint8_t>(140) }};  
  ws.binary();  
  ws.write(std::array<boost::asio::const_buffer, 1>{{  
    { err_buff.data(), err_buff.size() }  
  }});  
}  
```  
clang 4.0.1  
  
Calling it via the browser like this:  
```js  
const socket = new WebSocket('ws://localhost:3003');  
socket.addEventListener('message', (event) => {  
  console.log("Server sent: ", event.data);  
});  
socket.addEventListener('open', (event) => { socket.send("hello"); });  
```  
Results in: `failed: Could not decode a text frame as UTF-8`  
Changing the number from 140 to something below 128 avoids this error.  
Which makes me think it has something to do with interpreting the buffer in a certain way.  
  
It'b be great to have a fast response, as this is somewhat of a roadblock.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-09-10 19:16:12 UTC  
> Updated at: 2017-09-10 19:44:44 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-328364969  

140 is not a valid character for the first byte of a UTF-8 code point.  If you want to send binary data, set the message type to binary before sending the message data, like this:  
```  
socket.binary(true);  
```

---

## Comment 2

> Username: Voultapher  
> Created at: 2017-09-10 19:30:15 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-328365825  

Ok thanks, looking at `binary()` I misread the `==` for and `=`.

---

## Comment 3

> Username: Voultapher  
> Created at: 2017-09-10 19:31:30 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-328365909  

Btw, any reason you use `std::bind` over lambdas? The latter is both cleaner and produces better assembly.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-09-10 19:46:04 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-328366774  

The generated assembly should be no different. Can you give me an example of a piece of existing example code, and how you think it should look? Keep in mind, it has to be C++11 only (so no `auto` parameters in lambdas, or capture expressions).

---

## Comment 5

> Username: Voultapher  
> Created at: 2017-09-10 20:04:32 UTC  
> Updated at: 2017-09-10 20:05:29 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-328368055  

Ok, so `std::bind` is not as bad as I had in memory. Given C++11 as requirement there is [some reason for using it](https://stackoverflow.com/a/17545183).  
However lambdas are cleaner as they are a direct compiler construct and are aloud to have no size. [Also if called improperly can lead to loss of performance](https://stackoverflow.com/questions/24852764/stdbind-vs-lambda-performance).

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-09-11 02:11:31 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-328397672  

So I should stick with the `std::bind`? Do you want to provide an example of a lambda at a call site from one of the beast example programs?

---

## Comment 7

> Username: Voultapher  
> Created at: 2017-09-11 10:54:51 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-328494631  

Wherever possible use lambdas, as it also avoids the the `<functional>` dependency.  
[Small self contained example](https://godbolt.org/g/5h4Hto)  
Try replacing `ret_a` with a constant.  
  
From the websocket_async_server example:  
  
```cpp  
void run()  
{  
  ws_.async_accept(  
    strand_.wrap(  
      std::bind(  
        &session::on_accept,  
        shared_from_this(),  
        std::placeholders::_1  
      )  
    )  
  );  
}  
```  
Improved version  
```cpp  
void run()  
{  
  ws_async_accept(  
    strand_.wrap(  
      [this](boost::system::error_code ec) { on_accept(ec); }  
    )  
  );  
}  
```  
  
Completely unrelated, how would you suggest to do a websocket broadcast? Should I keep track of all the sessions myself?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-09-11 14:11:47 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-328541452  

>`[this](boost::system::error_code ec) { on_accept(ec); }`  
  
That looks very nice! There's just one problem, this will cause the socket to close prematurely. The original code binds a copy of `shared_from_this()` into the call wrapper, while the new version does not.  
  
> how would you suggest to do a websocket broadcast? Should I keep track of all the sessions myself?  
  
Yes. And you probably will need to implement an outgoing message queue, to handle the case where you want to broadcast while there is already an asynchronous write in progress on a stream. Also, remember that websocket streams (like asio sockets) are **not thread safe**. You can't call `async_write` from foreign threads. You need to call `async_write` from the same implicit or explicit strand.  
  
In practice, this means that when you want to send a message on a stream from a foreign thread, you need to call `io_service::post` with a completion handler that does the actual send.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-09-15 14:48:38 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-329804371  

Okay, are you all squared up now? Is there still something that needs attention?

---

## Comment 10

> Username: Voultapher  
> Created at: 2017-09-15 19:09:01 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-329873462  

All good. However I'd like to hijack this thread and give you the opportunity to stop me from showcasing anything blatantly wrong. Same as you, I'm presenting a talk at this years cppcon. It focuses heavily on websockets, so one of the libraries I'll compare is beast. You can find my current example implementation [here](https://github.com/Voultapher/Presentations/blob/master/cppcon2017WebService/example/server/beast/src/main.cpp). Ignore the `poll_data_.result.ref()` related UB, It'll be fixed.  
  
If you are busy or don't want to take a look. That's totally fine.  
Thanks for the great help so far!

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-09-15 19:38:09 UTC  
> Updated at: 2017-09-15 19:42:17 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-329879834  

I had a look at the repository. I could do a code review if you want, you just need to open up a pull request against your own branch so that I can comment. With respect to your presentation, I have to remind you that Beast is a low-level library. It does not try to implement a client or server in a few lines of code. Instead, it is a thin protocol layering on top of Boost.Asio. It is the library that someone might use to write a higher-level library which DOES implement a simple interface to produce a server or web service. This goes for the HTTP interfaces as well as the WebSocket interfaces. As you noted, Beast does not have WebSocket broadcast facilities built-in to it and likely never will, since the management of individual connections is beyond the scope of the library. But hopefully someone will write a higher level library on top of it.  
  
Also note that Beast utilizes abstractions in a way that other network libraries like uWebSockets do not. For example, you can create a websocket stream around any object that implements the read and write stream requirements. This lets someone easily use an alternative SSL implementation for example. Or you can do HTTP I/O through `std::ostream` and `std::istream` derived classes.

---

## Comment 12

> Username: Voultapher  
> Created at: 2017-09-16 14:17:57 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-329971143  

I'll try my best to convey that beast is not a high level abstraction, much rather a toolkit to implement such, and by that, tries to achieve a different goal and should be compared accordingly.  
  
A code review would be great. The 2 best ways I found to create a review of a single file. Is to either create a pull from the earliest non related commit like [this](https://github.com/Voultapher/Presentations/pull/1). Note there is a lot of clutter this way, as every other change is also included. Or you could comment on the individual commits found via the [blame overview](https://github.com/Voultapher/Presentations/blame/master/cppcon2017WebService/example/server/beast/src/main.cpp).

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-09-16 15:01:01 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-329973702  

It is a goal of beast to be proposed for standardization in C++. i.e. `std::http::message`, `std::websocket::stream`. The design of the library is based on this outcome.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-09-20 03:13:53 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-330733392  

Nothing bad jumped out from looking over your code

---

## Comment 15

> Username: Voultapher  
> Created at: 2017-09-21 16:40:17 UTC  
> Url: https://github.com/boostorg/beast/issues/776#issuecomment-331213634  

Great! Thanks for your time. I'll close this now, see you at cppcon.
