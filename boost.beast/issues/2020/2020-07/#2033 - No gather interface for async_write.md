# #2033 - No gather interface for async_write [Open]

> Username: haelix888  
> Created at: 2020-07-22 18:08:22 UTC  
> Updated at: 2024-06-06 05:34:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2033  

This is a request for confirmation, but if confirmed, it would be a nice feature request.  
  
At the Boost.Asio TCP level we have a "gather" interface which makes it possible to `async_write` data from multiple buffers at once - see Scatter/Gather here:  
https://www.boost.org/doc/libs/1_66_0/doc/html/boost_asio/reference/buffer.html  
  
However, at the WS level, there doesn't seem to be a way to do this. The WS `async_write` takes a `ConstBufferSequence` which may contain one message payload scattered across multiple buffers, is there a way to pass multiple message payloads in one function call? Apols if I am missing it.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-07-22 18:14:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662607147  

Great question.  
  
No, there's no way to scatter/gather at the message level.   
  
I'm not sure how we'd define that interface.  
  
There's nothing to stop you writing a composed operation that would do this, using asio::async_compose and asio::coroutine would make it fairly straightforward (there are some examples in beast code).  
  
However, some design questions:  
  
what do you return for "bytes_transferred" if only half the messages are sent?   
do you need to pass in a "results" object that can collect the results of each individual send?  
How to express the frames to send? a vector of one buffer per frame, or a vector of vector-of-buffers per frame?  
  
I imagine you can see why we didn't want to open this can of worms in a public-facing library :)

---

## Comment 2

> Username: haelix888  
> Created at: 2020-07-22 18:22:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662610977  

Totally agree. I admit, I have not thought about a suitable interface.  
But at the very least, the present completion handler doesn't suffice. Neither does text/binary designation, which would need to pe per-message (although an acceptable compromise would be to require uniformity among messages sent).  
My inclination would be to disallow partial writes of the array of messages, just like partial writes when gathering cannot happen (or maybe they can? not enough experience). In that case the interface would merely need to separate among the N payloads, in order to do the framing (?) - again, an assumption.  
cheers for the awesome reply. I don't know enough about composed writes to understand that part.

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-07-22 18:28:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662613999  

basically a loop expressed as a coroutine.

---

## Comment 4

> Username: haelix888  
> Created at: 2020-07-22 18:37:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662618269  

OK but that doesn't sound like having the potential to be doing one gather (one call to `writev`).  
(I realise that's a lot to ask)

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-07-22 18:52:26 UTC  
> Updated at: 2020-07-22 18:53:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662625743  

Here's a very simplified example:  
  
https://godbolt.org/z/5a498e  
  
```  
#include <boost/asio.hpp>  
#include <boost/asio/coroutine.hpp>  
#include <boost/beast/websocket.hpp>  
#include <vector>  
#include <string>  
#include <memory>  
#include <iostream>  
  
using frame_vector = std::vector<std::string>;  
using websock = boost::beast::websocket::stream<boost::asio::ip::tcp::socket>;  
  
template<class NextLayer, class CompletionToken>  
auto async_write_frames(  
    boost::beast::websocket::stream<NextLayer>& ws,   
    frame_vector const& frames,   
    CompletionToken&& tok)  
{  
    auto op = [&ws, &frames, iter = frames.begin(), coro = boost::asio::coroutine()]  
        (auto& self,   
         boost::beast::error_code ec = {},   
         std::size_t = 0) mutable  
    {  
        BOOST_ASIO_CORO_REENTER(coro)   
        for(;;)  
        {  
            if (iter == frames.end())  
                return self.complete(ec);  
  
            BOOST_ASIO_CORO_YIELD  
            ws.async_write(boost::asio::buffer(*iter), std::move(self));  
  
            if (ec)  
                return self.complete(ec);  
  
            ++iter;  
        }  
    };  
  
    return boost::asio::async_compose<  
        CompletionToken,   
        void(boost::beast::error_code)>(  
            std::move(op),   
            tok,  
            ws);  
}  
  
  
void test(websock& ws, std::shared_ptr<frame_vector> pframes)  
{  
    async_write_frames(ws, *pframes, [pframes](boost::beast::error_code ec)  
    {  
        if (ec)  
            std::cout << "not all written\n";  
        else  
            std::cout << "all written\n";  
    });  
}  
```

---

## Comment 6

> Username: madmongo1  
> Created at: 2020-07-22 18:55:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662627205  

*nb `frames` is a buffer containing the data to send. It must outlive the composed op, which is why I capture it via shared_ptr in the completion handler.

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-07-22 19:05:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662631947  

Good point over the single writev. It wouldn't help you with WSS in any case. OpenSSL doesn't support scatter/gather.  
In addition, websocket protocol is a framing protocol, with the length of the frame segment in the header, so this defeats scatter/gather for the entire protocol even unencrypted.  
  
We mitigate this by trying to read as much as possible at a time before decoding the frame.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-07-22 19:05:34 UTC  
> Updated at: 2020-07-22 19:05:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662632154  

You can't write multiple messages at once, but I agree that this is a feature that would be useful. The compromise is that the API will require that each message is in a single contiguous buffer. So in effect we would have a range of `T` where `T` is convertible to `net::const_buffer`. The implementation can coalesce all the outgoing messages into a single write, which would have performance benefits.

---

## Comment 9

> Username: haelix888  
> Created at: 2020-07-22 19:54:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662662912  

@madmongo1 about the framing - as far as I can imagine, the implementation could add up to-gather buffer sizes to obtain the total, even in the case where framing is imposed to be of a certain size. Then again, I don't know enough about the protocol, it's pure imagination. Interesting point about OpenSSL.  
  
@vinniefalco single buffer per message sounds good. My design is that there is a queue of buffers waiting to be `async_write` (only one outstanding `async_write` possible) so when draining the queue, that would be the opportunity to gather. I think this queueing design is quite popular

---

## Comment 10

> Username: vinniefalco  
> Created at: 2020-07-22 20:24:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662677569  

> My design is that there is a queue of buffers  
  
Yes exactly. If you have say, a game sending X,Y,Z coordinate updates then you might want to batch them and send them efficiently in a single I/O.

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-07-22 20:41:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662685490  

I'm not sure this helps with websocket. Remember that intermediate routers are allowed to re-frame the data en-route.  
  
There's nothing high performance about the websocket protocol at all.  
  
If your game needs performance, it'll most likely use a custom UDP-based protocol (or at a push a lean TCP protocol).

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-07-22 21:46:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662714041  

It definitely helps and several browser games use this technique. There are no "intermediate routers" in the gaming use-case.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2020-07-22 21:48:12 UTC  
> Updated at: 2020-07-22 21:48:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-662714654  

Consider the case where a multi-threaded application is producing real-time "updates" which need to be sent as individual websocket messages. If the websocket is busy writing, these updates have to be queued. By the time the current write is complete, there may be more than 1 message queued up. Because asynchronous calls are very expensive, you want to do as much work as possible. With multiple messages in the queue, the best solution is to send all the queued messages in a single I/O. This is why batched websocket writes are helpful.

---

## Comment 14

> Username: stale[bot]  
> Created at: 2020-08-22 19:00:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-678678941  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 15

> Username: imerr  
> Created at: 2020-11-18 03:34:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-729367408  

I'd like to second @vinniefalco here  
A way to have an (async_)write_many method which takes a range with message-per-buffer would be great  
  
I've added websocket support to a low-level game networking library and the performance (non-ssl) compared to raw tcp is pretty bad since there's no way to group many messages into a single call  
For the moment I've added a "buffered write stream" in-between the websocket and tcp stream, which buffers up writes and once the buffer is full enough or a timeout is reached, the buffer is flushed to the underlying tcp stream - that increased throughput 4x, but moved the bottleneck from write syscalls to async call overhead (if anyone's got any less hacky suggestions I'd be happy to hear those aswell)  
  
And yeah, in an ideal world we'd batch multiple "messages" into one websocket message before it reaches the network code, but I (and likely many other people) have to work with the constraints they're given

---

## Comment 16

> Username: vinniefalco  
> Created at: 2020-11-18 04:08:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-729392034  

Something like this  
```  
template< class FwdIt >  
void websocket::write_v( FwdIt first, FwdIt last, error_code& ec );  
```  
  
Where `std::is_convertible< typename std::iterator_traits<FwdIt>::value_type, net::const_buffer >::value == true`

---

## Comment 17

> Username: madmongo1  
> Created at: 2020-11-18 05:06:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-729421542  

Just to be sure I know what everyone is asking for here:  
  
Are we looking for a function which will write a batch of websocket messages? Or are we actually looking for a websocket object which can just be given messages to send in arbitrary order and they will be sent as soon as possible?

---

## Comment 18

> Username: vinniefalco  
> Created at: 2020-11-18 05:19:33 UTC  
> Updated at: 2020-11-18 05:24:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-729434825  

`write_v` (and `async_write_v`) is an alternative to `write`. In other words it counts as a write operation. But instead of sending one message provided as a _ConstBufferSequence_, it sends zero or more messages where each message is in a `const_buffer`. In other words, each message has to be in a single buffer. It is not a queue. The implementation would have to allocate a single large buffer internally to compose the frame headers, frame payloads, and possibly apply compression.  
  
When sending a vector of buffers there are no options for incremental sending or for storing each message in  more than one buffer.

---

## Comment 19

> Username: madmongo1  
> Created at: 2020-11-18 05:28:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-729437727  

I see. The intention is to save on syscalls at the expense of an allocation.  
Shouldn’t be a major issue. I expect there’s a lot of code from the synchronous path that can be reused here internally.

---

## Comment 20

> Username: iofo  
> Created at: 2022-03-10 21:10:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1064507803  

Did anything get done with this?  We have a bridge between UDP and web sockets.  We are using null_buffers and recvmmsg to get the UDP packets.  We then forward the UDP on to connected websocket clients.  Very often recvmmsg returns more than one message so it would be great to be able to write them all at once to the websockets using one syscall per client connected.  this is an internal application so we are not using WSS so any openssl considerations are moot.  
BTW thank you for creating beast it is awesome.

---

## Comment 21

> Username: madmongo1  
> Created at: 2022-03-10 21:40:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1064529810  

We didn't go any further with it at the time.  
However if it's compelling I'm happy to work on it.  
Do you have any idea of:  
1 - how many syscalls we are likely to save  
2 - Average size of each frame  
3 - number of frames being sent at a time  
4 - Now that io_uring is enabled in the underlying Asio code on enabled systems, are we gaining much?

---

## Comment 22

> Username: iofo  
> Created at: 2022-03-11 16:30:04 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1065277519  

This is obviously very specific to our use case, and I am an application developer so my system knowledge is limited so please forgive my ignorance here.  
  
1. see 3.  my understanding is that each websocket write_async call could result in a syscall lower down but my knowledge here is vague because I guess it depends on the lower layers and potential buffering.  
  
2. Around 350 bytes of data in this case.  
  
3. Here is a sample.  It is very rare that we want to write just one message.  Mostly it is 2 messages and then as bursts arrive we can potentially write order of 10's of messages in one go.  
  
count   nummsgs  
         78 1  
3789331 2  
 793933 3  
 258335 4  
  87479 5  
  41321 6  
  19922 7  
  11219 8  
   6446 9  
   3930 10  
   2407 11  
   1633 12  
   1057 13  
    758 14  
    515 15  
    353 16  
    236 17  
    187 18  
    129 19  
    102 20  
     72 21  
     49 22  
     38 23  
     33 24  
     26 25  
     17 26  
     16 27  
     15 28  
     14 29  
     10 30  
.. truncated  
  
4. I don't know much about io_uring so I will investigate this but regardless of lower layer implementation details having a batch send interface for beast websocket would be nice IMHO.  
  
Vinnie's comment above certainly applies to our use case where once we have one write outstanding we will start to queue other incoming messages so the next write we could send a batch but we also have the case where up front we have received multiple packets in one return from recvmmsg so even if no write is outstanding we have a batch to send.  
  
We could  also tcp cork the connection whilst we know we have more data so that we can be more optimal in sending full wire packets.   But I don't know how under asio/beast we would tell the lower layers to cork.  
  
We could implement our own framing so we write multiple UDP packets into one websocket message but it is preferable to retain the message boundaries.  
  
If we know we have two or more messages to send the vast majority of the time it would make sense to be able to send those with one write syscall so the OS at least has the opportunity to put the data in one packet.  
  
Thanks

---

## Comment 23

> Username: madmongo1  
> Created at: 2022-03-11 16:38:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1065284120  

Ok, so quite a few multi-message sends.  
Well ok, let’s try it. I’ll implement it on a branch and we can see if it helps.

---

## Comment 24

> Username: iofo  
> Created at: 2022-03-11 16:49:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1065295164  

> Just to be sure I know what everyone is asking for here:  
>   
> Are we looking for a function which will write a batch of websocket messages? Or are we actually looking for a websocket object which can just be given messages to send in arbitrary order and they will be sent as soon as possible?  
  
And also in response to this we need to retain ordering so it would be a function interface to send a sequence of messages.  
Also seeing as we have an incoming sequence of mmsghdr/iovec from recvmmsg and writev takes an iovec could we not just put the websocket headers in separate iovec entries so this could be a zero copy interface in the case of no per-message deflate and no SSL.  If some kind of MessageSequence concept existed that we could actually adapt over the top of struct mmsghdr * and the associated iovecs we could have an extremely efficient zero copy interface to go from UDP to websockets.   I have no idea about the internals here so maybe this is not possible and it maybe an optimization for a very rare use case.

---

## Comment 25

> Username: madmongo1  
> Created at: 2022-03-11 17:16:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1065317978  

`struct iovec` is very similar to `asio::const_buffer` and `asio::mutable_buffer`. The `readv` library call takes a pointer to an array of `struct iovec`, which in asio is a model of a `BufferSequence` concept.  
  
I think this will be fairly easy to make compatible.

---

## Comment 26

> Username: madmongo1  
> Created at: 2022-03-11 17:35:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1065333094  

>  could we not just put the websocket headers in separate iovec entries so this could be a zero copy interface in the case of no per-message deflate and no SSL.  
  
Possibly when transmitting from client to server, but when transmitting the other way, all data in the message is encrypted (by  XOR with a pre-negotiated key). Thus a copy, or at least a transform, is required.  
  
Websocket is not an efficient protocol by any means.

---

## Comment 27

> Username: iofo  
> Created at: 2022-03-11 17:36:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1065334001  

Yes, so on this note re readv it would also be awesome to be able to read batches of messages from a websocket if the underlying layer delivers up buffers that have multiple parseable full websockets messages then handing them to the client in one go as a sequence of messages could have performance benefits too.  So in my case where we are usually sending 2-3 messages in one go, the client on the other end could benefit from being handed these messages as a sequence in one call to their async_read_multiple (?) completion handler.  Again not sure how feasible/useful in the general case this is.

---

## Comment 28

> Username: iofo  
> Created at: 2022-03-11 17:38:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1065334902  

> > could we not just put the websocket headers in separate iovec entries so this could be a zero copy interface in the case of no per-message deflate and no SSL.  
>   
> Possibly when transmitting from client to server, but when transmitting the other way, all data in the message is encrypted (by XOR with a pre-negotiated key). Thus a copy, or at least a transform, is required.  
>   
> Websocket is not an efficient protocol by any means.  
  
ah yes this is a good point.  if we are not allowed to mutate the incoming buffer then copy is needed.  in our case we don't care if the incoming buffer is mutated but maybe some users would care.

---

## Comment 29

> Username: iofo  
> Created at: 2022-03-11 17:42:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1065338211  

>   
  
although actually now I think about it we cannot mutate because in the case of broadcasting the same data to many clients who have different XOR keys we cannot reuse the same buffers for each client if we are mutating it.  That is a nuisance.  It seems like a common thing to do with websockets as well which is broadcast the same message to many connected clients.

---

## Comment 30

> Username: madmongo1  
> Created at: 2022-03-11 19:08:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1065410468  

I'm not an expert on the origins of Websocket, but my impression is that it was invented as a hack to allow web browsers to defeat corporate firewalls when creating persistent connections to external servers, rather than an efficient internet transport mechanism.

---

## Comment 31

> Username: vinniefalco  
> Created at: 2022-03-16 15:11:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1069233950  

> are we actually looking for a websocket object which can just be given messages to send in arbitrary order  
  
The Websocket protocol guarantees ordering. So ordering must always be preserved.

---

## Comment 32

> Username: vinniefalco  
> Created at: 2022-03-16 15:13:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1069236448  

> XOR  
  
The client sends masked messages to the server, not the other way around.

---

## Comment 33

> Username: dufferzafar  
> Created at: 2023-07-12 18:31:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2033#issuecomment-1633020387  

> Vinnie's comment above certainly applies to our use case where once we have one write outstanding we will start to queue other incoming messages so the next write we could send a batch  
  
We too are facing a similar use case where we have a queue of messages to send per client & before we can write them all, fresh set of messages come in as a result of which the queue size keeps increasing.  
  
I feel scheduling one async_write per queue item is hurting us and being able to test a "large" write with the entire queue contents would be good.  
  
@madmongo1 Did you get around to implement this?   
  
If not, what is the currently suggested approach here?  
  
Tangentially related, I found this [SO Answer](https://stackoverflow.com/a/48043209) which uses scatter-gather async_write over a TCP stream, which looks like what we want. But over websockets!
