# #1278 - Support work on top of other network libraries. [Closed]

> Username: kotbegemot  
> Created at: 2018-10-28 08:03:07 UTC  
> Updated at: 2018-11-28 17:19:17 UTC  
> Closed at: 2018-11-28 17:19:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1278  

I have a number of projects that use a lot of boost(1.66-1.68).  
Projects are not all on asio as a network layer .  
Some Projects are using the implementation of network layer is a combination of libraries: libev + libcurl.  
Offer:  
Support other network libraries if possible: libev and libuv.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-28 23:54:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1278#issuecomment-433754383  

> Support other network libraries if possible: libev and libuv.  
  
Hmm....I don't think that's a good idea. Beast is written to work with Networking TS, which will become part of the standard library. One that happens, there is really little reason to use other network libraries since C++ will finally have a standard way to network.

---

## Comment 2

> Username: kotbegemot  
> Created at: 2018-10-30 06:00:51 UTC  
> Updated at: 2018-10-30 06:01:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1278#issuecomment-434181783  

Not all developers can use the latest standard.  
Developers often use boost(and the latter 1.66-1.xx).  
There is a decrease in the audience of developers who use boost .  
There is a possibility that not everyone will use Networking TS.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-11-04 02:27:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1278#issuecomment-435636773  

> There is a possibility that not everyone will use Networking TS.  
  
That may be true, but I am just one developer. I can't support multiple networking libraries, as I have plans to continue building on top of Beast. However, there are other libraries which adapt libev and libuv to Asio concepts such as *AsyncReadStream* and *AsyncWriteStream*. With these libraries, those network interfaces can be used with Beast. And since they are maintained by people who know those network libraries well they will be of a higher quality than anything I can create.  
  
However, I believe that once Networking TS has gone into C++, that use of other TCP/IP network libraries will decrease and eventually disappear. Why would anyone use something external when it is already in the standard library? I don't see why they would.

---

## Comment 4

> Username: kotbegemot  
> Created at: 2018-11-09 08:15:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1278#issuecomment-437283407  

I suggest we return to this issue later and close the task.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-11-28 17:19:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1278#issuecomment-442530087  

Support for other networking libraries is considerably out-of-scope for Beast and will likely never happen. However, the `http::message` container, and the `parser` and `serializer` classes should still be usable (with <boost/asio/buffer.hpp>). You would just have to implement the stream algorithms.
