# #667 - [Question] Session security with Beast [Closed]

> Username: djarek  
> Created at: 2017-07-21 01:19:11 UTC  
> Updated at: 2019-04-10 11:27:31 UTC  
> Closed at: 2017-08-16 00:08:54 UTC  
> Url: https://github.com/boostorg/beast/issues/667  

Hi,  
I've been playing around recently with using Beast to implement Apple's HomeKit (since they released a non-commercial spec recently) and I've ran into an issue of implementing its session security. It requires me to prepend some binary data(message length) before the HTTP request/response header and append some at the end (cryptographic MAC). The question I'd like to ask is how would I do this best with Beast? Should I write my own stream socket that wraps an ASIO TCP socket or would it be better to to provide custom Beast serializers? I'd like to avoid having to copy the serialized HTTP message just to construct the session layer PDU.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-21 01:22:45 UTC  
> Updated at: 2017-07-21 01:35:57 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-316875142  

```  
template<class Stream, bool isRequest, class Body, class Fields>  
void write_homekit(Stream& stream, beast::http::message<isRequest, Body, Fields> const& m)  
{  
    beast::http::serializer<isRequest, Body, Fields> sr{m};  
    prepend_stuff(stream, m);  
    beast::http::write_header(stream, sr);  
    append_stuff(stream, m);  
    beast::http::write(stream, sr);  
}  
```  
  
`prepend_stuff` and `append_stuff` are functions you provide.  
  
Hope this helps!

---

## Comment 2

> Username: djarek  
> Created at: 2017-07-23 13:16:50 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-317252467  

@vinniefalco   
What about an async version of this? Would I need a custom composed operation? Also, I need to prepend the length of the entire HTTP message(header + content) and encrypt the HTTP message (which means I need it stored in a mutable buffer, so it's hard if I wanted to provide a custom socket with my own async_write_some).

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-23 14:38:04 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-317257461  

You don't need your own custom socket. You can serialize the message into a string using `operator<<`, for example:  
```  
std::stringstream ss;  
ss << req;  
```  
  
You will need a small composed operation but it is trivial, use the simplified form of composed operation as illustrated here:  
http://vinniefalco.github.io/beast/beast/using_i_o/example_detect_ssl.html  
  
Make `std::shared_ptr<std::string> s_` a data member of your composed operation, its the result of `operator<<` to the `std::stringstream`. Just call `boost::asio::async_write` on the string. When you have something that compiles, post it here and I will audit it for you.  
  
By the way, Homekit's requirement to prefix the message with the length means that the entire object must be rendered in memory which is quite a burden.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-07-27 19:18:43 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-318460455  

Has this issue been resolved to your satisfaction?

---

## Comment 5

> Username: djarek  
> Created at: 2017-07-30 20:13:30 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-318926438  

I tried the approach you suggested and it works... for messages that are smaller than about 1024 bytes. I missed the part in the spec that said the encrypted messages have to be fragmented to be no larger than IPv6 minimal MTU :/. This means that one HTTP request/response may be fragmented into multiple  "HomeKit messages", e.g.:  
```  
<2 bytes LE, fragment length>  
PUT /characteristics HTTP/1.1  
Host: mydevice.local  
Accept-Language: en-us  
Accept-Encoding: gzip, deflate  
[...]  
<16 bytes of auth tag>  
<2 bytes LE, fragment length>  
Connection: Keep-Alive  
[...]  
<16 bytes of auth tag>  
```  
  
Of course, the "payload" of these fragments would normally be encrypted, I skipped that for brevity.  
Do I have to use beast's ```parser``` directly with my own state machine to support this kind of fragmentation, or is there some way of "hooking up" custom functionality beneath existing beast read operations?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-07-30 20:21:50 UTC  
> Updated at: 2017-07-30 20:25:28 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-318927013  

>I missed the part in the spec that said the encrypted messages have to be fragmented to be no larger than IPv6 minimal MTU :/. This means that one HTTP request/response may be fragmented into multiple "HomeKit messages", e.g.:  
  
Ah, so its not the case that the HTTP header has to be prefixed and suffixed, but rather, the serialized HTTP message needs to be broken up into packets (at any boundary) with each packet containing a header and encrypted payload?  
  
>is there some way of "hooking up" custom functionality beneath existing beast read operations?  
  
Yes, this is quite easy just implement your own **Stream** which wraps a socket, and invoke your HTTP operations on that. There are a few examples in both Beast and Boost:  
  
Here's an example of a wrapper that does read buffering:  
https://github.com/boostorg/beast/blob/e0f0dc5bca8ac2c3fddd563fce37e3de8d4592c9/include/boost/beast/core/buffered_read_stream.hpp#L26  
  
This stream is used for testing:  
https://github.com/boostorg/beast/blob/e0f0dc5bca8ac2c3fddd563fce37e3de8d4592c9/extras/boost/beast/test/pipe_stream.hpp#L71  
  
This Asio stream does read and write buffering:  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/buffered_stream.hpp#L45  
  
The tricky part will be writing the asynchronous composed operations. You can start by implementing the synchronous versions and getting that working. Then you can study Beast's composed operation tutorial to learn how to convert it to an asynchronous operation:  
http://vinniefalco.github.io/beast/beast/using_i_o/writing_composed_operations.html  
  
I can help you with the asynchronous part. If you get this working, I would consider adding it to Beast because a low-level HomeKit stream wrapper is very much in scope for the library. I think other people would find it very useful, and it is general purpose - it would also work with WebSocket!  
  
If you go this route, consider forking the Beast repository and doing your work in a fork using the existing unit test framework, I can help you that way by keeping an eye out on your commits and offering tips. I think this is a very good project. I will add Travis support for building on OS X to make this easier.

---

## Comment 7

> Username: djarek  
> Created at: 2017-07-31 07:15:23 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-318988018  

> Ah, so its not the case that the HTTP header has to be prefixed and suffixed, but rather, the serialized HTTP message needs to be broken up into packets (at any boundary) with each packet containing a header and encrypted payload?  
  
Yes.  
  
> The tricky part will be writing the asynchronous composed operations. You can start by implementing the synchronous versions and getting that working. Then you can study Beast's composed operation tutorial to learn how to convert it to an asynchronous operation:  
  
I got the socket working with small messages. I'll try to check the communication with larger payloads using Apple's official tools and see how the fragmentation behaves. Unfortunately reading is a little inefficient, because I currently copy the data twice (once from a temporary to the socket's dynamic buffer and then to the caller supplied mutable buffer sequence to handle the situation when the caller supplies a buffer that is smaller than the received PDU's payload).   
  
> I can help you with the asynchronous part. If you get this working, I would consider adding it to Beast because a low-level HomeKit stream wrapper is very much in scope for the library. I think other people would find it very useful, and it is general purpose - it would also work with WebSocket!  
If you go this route, consider forking the Beast repository and doing your work in a fork using the existing unit test framework, I can help you that way by keeping an eye out on your commits and offering tips. I think this is a very good project. I will add Travis support for building on OS X to make this easier.  
  
Wow, haven't suspected this proof-of-concept project of mine would end up like this. Though, you should be aware that for the stream wrapper to be of any use at all it requires a lot of logic. The HomeKit session security layer is basically TLS with 99% of the features thrown away, no protocol encapsulation and the cryptographic handshake performed at the application layer (cryptographic key exchange is performed over an unsecure HTTP connection which is the upgraded into a HomeKit session).  
  
To sum up, if we wanted to have the low-level functionality of HomeKit session security in Beast, the bare minimum required is:  
- tlv8 parsing, serialization - this is easy, 100 LOCs with convenience overloads at best  
- client/server pair-setup state machine (SRP, HKDF, ChaCha20-Poly1305, ed25519) - establishes a first client's "certificate" in the key store  
- client/server pair-verify state machine (curve25519, HKDF, ChaCha20-Poly1305, ed25519) - establishes a secure session based on client "certificates" already present in the key store, analogous to a TLS handshake  
- HomeKit secure socket (ChaCha20-Poly1305)  
- client/server key store (manages long-term keys, simillar to TLS certificate store)  
  
The bare-minimum implementation I currently have, has around 2.5k lines of code for just the server side and with only an in-memory key store. And all this code is necessary to securely send  
```HTTP/1.1 501 Not Implemented```  
to the client! :D Another problem is the required crypto - I'm currently using wolfssl, due to the fact that openssl (libcrypto) does not have customizable hashing function and MGF in its implementation of SRP.  
  
Don't get me wrong - I'd love to contribute this piece of code, but I guess you'd have to decide how much of this functionality is in scope of Beast.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2017-07-31 14:20:39 UTC  
> Updated at: 2017-07-31 14:23:56 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-319081263  

UGH...you're right of course. All the crypto would be nasty, I forgot that part. You're correct when you state that it is out of scope for Beast. I can still help you with your wrapper if you'd like, to get the composed operation parts right.  
  
>Unfortunately reading is a little inefficient, because I currently copy the data twice (once from a temporary to the socket's dynamic buffer and then to the caller supplied mutable buffer sequence to handle the situation when the caller supplies a buffer that is smaller than the received PDU's payload).  
  
Don't even sweat an extra `memcpy`. The cost of going through the kernel for `async_read_some` is orders of magnitude greater than the memory copying. Consider using `beast::static_buffer` if possible, its a fixed size circular buffer which performs no dynamic allocation. Avoid small reads: don't issue a call to read the 8-byte header, or however big it is, call `async_read_some` instead with a decent sized circular buffer (1600 bytes for example) and just process whatever comes in, maybe issuing another call to read if you didn't get enough bytes. I added a small fixed size buffer to `websocket::stream` for this reason. Reads **always** go through the extra buffer instead of going directly to the caller's buffer. The read performance is now 200% faster.  
  
> in-memory key store  
  
There's a key store? This gets worse and worse lol  
  
Although out of scope for Beast, you could turn this into its own stand-alone library with tests, Travis, and code coverage, if you are so inclined. If you go this route I can help you with the Travis and the test infrastructure.

---

## Comment 9

> Username: djarek  
> Created at: 2017-08-02 22:30:40 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-319816847  

Hi,  
  
> Although out of scope for Beast, you could turn this into its own stand-alone library with tests, Travis, and code coverage, if you are so inclined. If you go this route I can help you with the Travis and the test infrastructure.  
  
Thanks a lot for the offer, I really appreciate it! I'll try to get the code cleaned up a little so that people don't die just by looking at it... proof-of-concept projects tend to be a little messy :D.  
  
For now, I'd like to ask for some advice on structuring the project. I really like how Beast is structured and I'm trying to imitate it a little. I'd like to keep the library header only - it makes it very easy to work with and keeps most components generic, but I have a few places in which I have free-functions that aren't all that useful to be templated. As an example:  
```C++  
bool aead_decrypt(gsl::span<const gsl::byte, aead_key_size> key,  
                  gsl::span<const gsl::byte, aead_nonce_size> nonce,  
                  gsl::span<const gsl::byte> aad,  
                  gsl::span<const gsl::byte> ciphertext,  
                  gsl::span<gsl::byte> message_out);  
```  
How would you recommend one should deal with such functions? Should I replace the span with template concepts, e.g. ContiguousConstBuffer, ContiguousMutableBuffer? Or should I give up on having a header only approach? These functions are mostly for internal use only - after all, I plan this project to be an abstraction of the hard and boring parts of HomeKit.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-08-02 23:54:14 UTC  
> Updated at: 2017-08-03 00:03:34 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-319829888  

>How would you recommend one should deal with such functions?  
  
Three easy solutions:  
  
1. Mark `aead_decrypt` as `inline`  
  
2. Gratuituously make `aead_decrypt` templated:  
  
```  
template<class = void>  
bool aead_decrypt(  
                  gsl::span<const gsl::byte, aead_key_size> key,  
                  gsl::span<const gsl::byte, aead_nonce_size> nonce,  
                  gsl::span<const gsl::byte> aad,  
                  gsl::span<const gsl::byte> ciphertext,  
                  gsl::span<gsl::byte> message_out);  
```  
  
or  
  
3. Make `aead_decrypt` an `inline` function that calls a gratuituously templated private function.  
  
```  
namespace detail {  
template<class = void>  
bool aead_decrypt(  
                  gsl::span<const gsl::byte, aead_key_size> key,  
                  gsl::span<const gsl::byte, aead_nonce_size> nonce,  
                  gsl::span<const gsl::byte> aad,  
                  gsl::span<const gsl::byte> ciphertext,  
                  gsl::span<gsl::byte> message_out);  
} // detail  
  
inline  
bool aead_decrypt(  
                  gsl::span<const gsl::byte, aead_key_size> key,  
                  gsl::span<const gsl::byte, aead_nonce_size> nonce,  
                  gsl::span<const gsl::byte> aad,  
                  gsl::span<const gsl::byte> ciphertext,  
                  gsl::span<gsl::byte> message_out)  
{  
    return detail::aead_decrypt(key, nonce, aad, ciphertext, message_out);  
}  
```  
  
When you write library code sometimes you have to do more work or weird things, but the sacrifice is worth it because the user of the library gets a polished result. In this case the benefit of choice number **3** is that if they take the address of the public function then it will work for them, and they can't misuse it as they could with **2** by instantiating it with something other than `void` (which is meaningless)  
  
I prefer **3** myself simply because in some cases a compiler will inline **1** needlessly.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-08-02 23:57:28 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-319830322  

> I really like how Beast is structured and I'm trying to imitate it a little.   
  
You're following a time-honored tradition here, almost everything in Beast imitates some other project (Asio mostly)

---

## Comment 12

> Username: djarek  
> Created at: 2017-08-06 19:44:43 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-320527981  

It seems that the async operations I wrote based on your instructions are working, could you take a look and see if if there's something obviously wrong with them?  
- [async_read_op](https://github.com/djarek/gabia/blob/master/include/gabia/secure_socket.hpp#L197)   
- [async_write_op](https://github.com/djarek/gabia/blob/master/include/gabia/secure_socket.hpp#L172)  
  
How should exceptions be handled inside composed operations?

---

## Comment 13

> Username: vinniefalco  
> Created at: 2017-08-06 19:47:09 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-320528130  

I see problems, if you can somehow make a pull request against one of your own repositories, for these two files, then I can add review comments.

---

## Comment 14

> Username: vinniefalco  
> Created at: 2017-08-06 19:57:10 UTC  
> Updated at: 2017-08-06 19:57:27 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-320528717  

Try this, back up your local repository, then force-push an empty "Initial commit" to your GitHub repo. Then submit a pull request from a different branch to your main branch via GitHub that adds the file secure_socket.hpp, then I will be able to leave review comments. And boy do I have a lot to say :)

---

## Comment 15

> Username: djarek  
> Created at: 2017-08-06 20:03:31 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-320529101  

You should be able to leave comments in that file: https://github.com/djarek/gabia/pull/1  
TBH I don't wanna risk playing around with force-pushes like that :D.

---

## Comment 16

> Username: djarek  
> Created at: 2017-08-06 20:05:43 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-320529216  

Ok I see you can't leave comments in unrelated lines... I'll try to fix it asap.

---

## Comment 17

> Username: djarek  
> Created at: 2017-08-06 20:13:48 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-320529672  

This should work https://github.com/djarek/gabia/pull/2

---

## Comment 18

> Username: vinniefalco  
> Created at: 2017-08-16 00:00:42 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-322621411  

Has this issue been resolved?

---

## Comment 19

> Username: djarek  
> Created at: 2017-08-16 00:08:54 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-322622553  

I might have some more questions in the future, but I'll close this issue for now.

---

## Comment 20

> Username: dFohlen  
> Created at: 2019-04-04 15:47:52 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-479953689  

@vinniefalco thanks a lot for the implementation of Beast.   
  
Currently I'm also try to realize a Boost::Beast server for Apple HomeKit.   
Is there now an easier way to parse and send encrypted HTTP messages?  
  
I would prefer if I could use the example of websockets/chat-multi. First setup the unencrypted connection via HTTP and then upgrade to a permanent HTTP connection with queue and encrypted messages. Because HomeKit needs also to handle multiple sessions.  
  
The first step works like a charm, but now I'm having trouble making the persistent connection, reading the encrypted request and then parsing it.  
  
I hope you can help me a bit.

---

## Comment 21

> Username: vinniefalco  
> Created at: 2019-04-04 16:38:18 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-479972882  

@djarek ?

---

## Comment 22

> Username: djarek  
> Created at: 2019-04-04 18:55:12 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-480020789  

@FohlenAFK the best approach is to write a stream wrapper that allows enabling encryption after pair-verify completes successfully. Here's an example of how that might work: https://github.com/djarek/gabia/blob/master/include/gabia/secure_socket.hpp#L90. Note that this code hasn't been updated for a long time (no longer have access to HAT) and most likely doesn't compile anymore with recent boost versions and it implements an older version of HomeKit (compatible with iOS 10 IIRC). Obviously, that repository doesn't contain the secret sauce needed to support the Apple MFi Chip, because NDAs :). Other than that, the implementation of the crypto is complete (key management is in-memory, but should be easy to retrofit something that suits your needs).  
  
As for using the websockets/chat-multi example: unfortunately, that might not work because the HomeKit spec only requires HTTP/1.1 for IP devices, so a websocket connection will never come from the client.

---

## Comment 23

> Username: dFohlen  
> Created at: 2019-04-05 08:58:46 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-480201695  

Thanks @djarek for the fast respond and the link to your secure_socket implementation.  
  
I am aware that no websocket connection can be made, only the structure is exactly what I need. The HomeKit bridge must handle up to 16 concurrent connections. The hardest part is sending events from an external thread to all clients, but only after they are no longer in a request handle. The shared_state example from Beast would do this.  
  
I'll take a closer look at your repository and probably implement a mix of both.

---

## Comment 24

> Username: djarek  
> Created at: 2019-04-05 09:15:19 UTC  
> Updated at: 2019-04-05 09:15:24 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-480206463  

> The HomeKit bridge must handle up to 16 concurrent connections.  
  
Unless, something changed, the spec requires 16 pairings, but the number of concurrent connections required is 8(and the next connection is allowed to close the least used one). The concurrent connection requirement wasn't explicit, but it can be deduced out of Apple's certification test cases :).

---

## Comment 25

> Username: dFohlen  
> Created at: 2019-04-10 08:37:20 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-481595781  

Hi @djarek,  
  
you were right! From the HAP protocol specification:  
  
> Must support restoring sessions for at-least 8 sessions  
  
I am now finished with the most part, the server is now able to send and receive encrypted messages, so the pairing works.  
Now, I would like to send a message to all clients from an outside thread. For this I use the example of `chat-multi` with `shared_state`. However, I do not have access to the listener instance to access the set of sessions. Is there an approach to this?

---

## Comment 26

> Username: djarek  
> Created at: 2019-04-10 11:27:05 UTC  
> Updated at: 2019-04-10 11:27:31 UTC  
> Url: https://github.com/boostorg/beast/issues/667#issuecomment-481651476  

Since you'll need to do some sort of event aggregation (Apple doesn't allow sending events more often than once per second to a particular client), the simplest solution is to have a component that tracks open connections and does the aggregation, so the algorithm for handling an event is:  
  
  
1. if event timer started, goto 3  
2. start the event timer (1 second), once it expires, send aggregated events to all registered clients.  
3. add event to the set, replacing a currently existing value if it's already there (event set is a map of `(aid,iid) -> value`).  
  
So the tracking component would be something like this:  
```  
struct event_dispatcher  
{  
  void subscribe(client& c);  
  void unsubscribe(client& c);  
  void send_event(event e);  
};  
```  
  
Where `event` is a data structure that represents the event, usually something like  
```  
struct event  
{  
  std::uint32_t aid;  
  std::uint32_t iid;  
  variant<bool, std::uint64_t, std::int64_t, double, std::string, std::vector<std::byte>> value;  
};  
```  
Note: `value` might be missing some types, fill-in as needed.
