# #807 - Assertion failed: (bytes_transferred > 0) [Closed]

> Username: mikea  
> Created at: 2017-10-05 22:03:37 UTC  
> Updated at: 2019-01-31 21:51:13 UTC  
> Closed at: 2019-01-31 21:51:13 UTC  
> Url: https://github.com/boostorg/beast/issues/807  

My program often crashes with the following beast assertion:  
  
`Assertion failed: (bytes_transferred > 0), function operator(), file xxxx/include/boost/beast/websocket/impl/read.ipp, line 436.`  
  
  
### Version of Beast  
  
120  
  
### All relevant compiler information  
  
```  
$ gcc --version  
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/usr/include/c++/4.2.1  
Apple LLVM version 8.0.0 (clang-800.0.42.1)  
Target: x86_64-apple-darwin16.7.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-05 22:13:13 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334606809  

Are you calling read with a buffer of zero bytes?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-10-05 22:16:13 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334607413  

What is your **NextLayer** type? Are you using `stream<ip::tcp::socket>`? SSL? Or something else?

---

## Comment 3

> Username: mikea  
> Created at: 2017-10-05 23:00:28 UTC  
> Updated at: 2017-10-05 23:00:42 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334615173  

I am using `  boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::ip::tcp::socket> >`  
  
I am always calling `_ws.async_read(` with the same buffer:  
  
```  
  constexpr int READ_BUFFER_SIZE = 100000;  
  boost::beast::multi_buffer _read_buffer{READ_BUFFER_SIZE};  
```  
  
and read it fully within `async_read` callback: `      _read_buffer.consume(_read_buffer.size());`

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-10-05 23:47:01 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334622103  

Check it out:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/read.ipp#L436  
  
The only way for `bytes_transferred==0` is for an error to occur, according to the contract for `async_read_some`:  
  
_"Initiates an asynchronous operation to read one or more bytes of data from the stream..."_  
  
Unless the destination buffer has zero length, which you said is not happening. And I wrote a test, the code can't get to line 436 when the buffer has zero length. I'm gonna need some help figuring this out, it could be something to do with how the OpenSSL stream works.  
  
Is there a way to reproduce it without using OpenSSL? What version of OpenSSL are you using?  
  
Thanks

---

## Comment 5

> Username: mikea  
> Created at: 2017-10-06 17:53:33 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334826108  

@vinniefalco no, I don't have a good reproducer. I see this from time to time on our client talking to a custom ws server.  
  
I am not sure why it comes back with `bytes_transferred == 0`. Maybe it is better not to crash but to handle it more gracefully?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-10-06 18:25:47 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334833927  

>Maybe it is better not to crash but to handle it more gracefully?  
  
In theory that is true but the reason the assert is there in the first place is to detect when the state of the websocket stream has broken invariants. If we don't know what is causing the broken invariants then we cannot gracefully handle the case.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-10-06 18:28:40 UTC  
> Updated at: 2017-10-06 18:28:52 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334834631  

Try adding this assert before line 431 (the `BOOST_ASIO_CORO_YIELD`)  
```  
BOOST_ASSERT(buffer_size(buffers_prefix(clamp(ws_.rd_remain_), cb_) > 0);  
```

---

## Comment 8

> Username: mikea  
> Created at: 2017-10-06 18:28:53 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334834669  

@vinniefalco then passing error to the user is a better solution than crashing.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2017-10-06 18:31:19 UTC  
> Updated at: 2017-10-06 18:31:40 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334835268  

>passing error to the user is a better solution than crashing.  
  
Hmm, no that's not true. Error codes are not used to signal broken invariants. And production code is not responsible for checking invariants - that's the responsibility of 1. the calling code and 2. the library author (to make sure that invariants are not broken). The asserts are there to inform the library author that something is wrong. We have to find out *why* the assert is happening, otherwise it cannot be fixed in a way that guarantees defined behavior.  
  
If you comment out the assertion, does your program behave normally?

---

## Comment 10

> Username: vinniefalco  
> Created at: 2017-10-06 18:32:42 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334835596  

Please try this branch: https://github.com/vinniefalco/beast/commits/v122

---

## Comment 11

> Username: vinniefalco  
> Created at: 2017-10-06 18:34:10 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-334836009  

>What version of OpenSSL are you using?

---

## Comment 12

> Username: mikea  
> Created at: 2017-10-10 18:35:29 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-335567358  

> What version of OpenSSL are you using?  
 1.1.0f

---

## Comment 13

> Username: mikea  
> Created at: 2017-10-10 18:42:39 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-335569305  

@vinniefalco I've deployed v122 version. Will report back soon.

---

## Comment 14

> Username: mikea  
> Created at: 2017-10-20 23:02:39 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-338341574  

@vinniefalco I still get the same error with 122 version:  
  
Assertion failed: (bytes_transferred > 0), function operator(), file boost/beast/websocket/impl/read.ipp, line 438.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2017-10-21 02:00:53 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-338356580  

hmm...this is very suspicious because according to the assert, the Asio implementation is breaking the invariant. According to the asio docs, `async_read_some` returns either when it has placed one or more bytes in the buffer, or when an error occurs. This assert implies that no bytes were transferred. But the condition `ws_.check_ok(ec)` is clearly `true` or else we would have not reached that line. The only explanation is that the invariant is being broken.  
  
The only possibilities for broken invariants in this case are:  
  
1. A bug in your version of OpenSSL  
2. A bug in the implementation of `ssl::stream` (what version of Asio are you on?)  
3. A bug in the networking driver  
4. You are using your own AsyncStream which breaks one or more invariants

---

## Comment 16

> Username: mikea  
> Created at: 2017-10-23 22:42:51 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-338818046  

made sure I am up-to-date everywhere I can:   
- Openssl 1.1.0f  
- Boost 1.65.1  
- Beast 123  
  
I don't use custom AsyncStream. My code follow ssl websocket client very closely.  
  
If the problem is in boost, maybe you can introduce invariant checking code in boost asio?  
In any case, I would really love to see this handled any way, but process crash. (maybe send a special error code?)

---

## Comment 17

> Username: vinniefalco  
> Created at: 2017-10-24 00:00:52 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-338830757  

>If the problem is in boost, maybe you can introduce invariant checking code in boost asio?  
  
I will open an issue now that the author is active  
  
>I would really love to see this handled any way, but process crash. (maybe send a special error code?)  
  
The problem is that we don't know what a return value of 0 from `ssl::stream::async_read_some` is supposed to represent. The documentation for ssl stream states:  
  
>"This function is used to asynchronously read one or more bytes of data from the stream."  
http://www.boost.org/doc/libs/1_65_1/doc/html/boost_asio/reference/ssl__stream/async_read_some.html  
  
If I add code to handle this in non-debug builds then it would need a test and I have no way to write that. I would be releasing code that affects all users without knowing under what conditions it will trigger. So I hope you understand why I am reluctant to do it.  
  
I have been searching the web for some kind of knowledge or bug report explaining why ssl returns 0 bytes from a read operation but I have not yet found anything. If you can find some information, that would be very helpful.

---

## Comment 18

> Username: stale[bot]  
> Created at: 2018-04-24 16:52:48 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-384003641  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 19

> Username: vinniefalco  
> Created at: 2018-04-24 22:52:58 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-384104693  

@mikea I believe this issue address your case: https://github.com/chriskohlhoff/asio/issues/298

---

## Comment 20

> Username: stale[bot]  
> Created at: 2018-05-24 22:56:30 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-391890858  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 21

> Username: stale[bot]  
> Created at: 2018-05-31 23:53:33 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-393717862  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 22

> Username: vinniefalco  
> Created at: 2018-12-18 15:59:56 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-448271196  

See: https://github.com/boostorg/beast/issues/1373#issuecomment-448271021

---

## Comment 23

> Username: stale[bot]  
> Created at: 2019-01-24 21:23:48 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-457361987  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 24

> Username: stale[bot]  
> Created at: 2019-01-31 21:51:11 UTC  
> Url: https://github.com/boostorg/beast/issues/807#issuecomment-459519820  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
