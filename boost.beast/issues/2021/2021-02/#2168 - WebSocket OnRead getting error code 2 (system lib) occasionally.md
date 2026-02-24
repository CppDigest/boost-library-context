# #2168 - WebSocket OnRead getting error code 2 (system lib) occasionally. [Closed]

> Username: ErikSod  
> Created at: 2021-02-22 18:21:36 UTC  
> Updated at: 2022-09-24 05:28:16 UTC  
> Closed at: 2022-09-24 05:28:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2168  

Version of Beast: 1.73.   
Compiler CoreXT 16.8.  
  
I'm using SSL websocket ( boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>> _webSocket) as a client to connect to a server endpoint and running into some occasional problems with OnRead. Most times it will work fine, but occasionally it will get all the way through the SSL handshake and connection but _webSocket.async_read will return error code 2 (system lib). I didn't see this error code in the websocket errors list so I wasn't sure what it could be caused by. Any ideas what could be happening here?

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-02-22 18:40:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-783586462  

Are you on linux or windows?

---

## Comment 2

> Username: ErikSod  
> Created at: 2021-02-22 19:09:26 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-783604713  

Windows.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-02-22 19:51:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-783630883  

1. What do you get if you print everything.message() to the console?  
  
2. are you sure you’re not accessing the websocket on 2 threads? Strange errors are often the result of a data race.

---

## Comment 4

> Username: ErikSod  
> Created at: 2021-02-22 20:55:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-783668673  

1. The message() is just "system lib".   
2. That's a good question, let me see if I can force just one thread and see if it repros.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:01 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-850857872  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: mhassanshafiq  
> Created at: 2021-11-09 03:00:00 UTC  
> Updated at: 2021-11-09 03:02:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-963768883  

@ErikSod @madmongo1 was this issue resolved? I am getting this as well, running boost 1.73 on linux  
  
generally observing this when opening a lot of connections.  
  
and I verified the read is happening in a single thread, I am using strand for all read, write and close functions

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-11-09 19:54:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-964485703  

Where does the string "system lib" come from? Did you check asio sources?

---

## Comment 8

> Username: mhassanshafiq  
> Created at: 2021-11-10 01:11:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-964689028  

I am not sure, just printing the `error.message()` where error is passed into the read callback.  
  
Searched the whole boost source, couldn't find this error string anywhere.  
@vinniefalco

---

## Comment 9

> Username: madmongo1  
> Created at: 2021-11-10 04:52:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-964785612  

At the moment I don’t have a lot to go on. Could you post a short complete program that exhibits the problem?  
  
At the moment I am suspecting a data race or use of freed memory (because that’s what these problems usually are). But without having something to test it’s guesswork.

---

## Comment 10

> Username: vinniefalco  
> Created at: 2021-11-10 15:30:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-965404296  

What is `error.category()`?

---

## Comment 11

> Username: mhassanshafiq  
> Created at: 2021-11-10 21:40:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-965771510  

error:system lib, category:asio.ssl  
  
@vinniefalco

---

## Comment 12

> Username: vinniefalco  
> Created at: 2021-11-10 22:47:59 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-965814748  

What happens when you use a plain TCP socket instead of an SSL stream?

---

## Comment 13

> Username: madmongo1  
> Created at: 2021-11-10 23:16:02 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-965829911  

FYI, from OpenSSL source:  
  
```  
 static ERR_STRING_DATA ERR_str_reasons[]=  
 {  
 {ERR_R_SYS_LIB              ,"system lib"},  
 ```  
  
Typical use in OpenSSL:  
  
```  
if (BIO_read_filename(in, file) <= 0) {  
 SSLerr(SSL_F_SSL_USE_CERTIFICATE_FILE, ERR_R_SYS_LIB);  
 goto end;  
```

---

## Comment 14

> Username: vinniefalco  
> Created at: 2021-11-10 23:58:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-965855987  

it might be nice to know the actual SSL error instead of just "system lib"

---

## Comment 15

> Username: mhassanshafiq  
> Created at: 2021-11-11 05:23:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-966000197  

@vinniefalco tested with simple tcp (instead of ssl) NOT getting the random disconnects with this.

---

## Comment 16

> Username: madmongo1  
> Created at: 2021-11-11 07:40:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-966061039  

Hi hate to day it but this kind of intermittent problem is normally due to a race condition or inadvertent undefined behaviour being introduced in the code.  
  
Is the program multi-threaded?

---

## Comment 17

> Username: mhassanshafiq  
> Created at: 2021-11-11 11:06:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-966211866  

yes, the code is multi threaded, but all the read write and close operations are posted to the same strand. @madmongo1

---

## Comment 18

> Username: madmongo1  
> Created at: 2021-11-11 16:35:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-966447150  

I would bet that if you moved just the comms code into its own single-threaded program you'd see no problems.  
  
Out of interest, can you confirm that the openssl library you are linking to compiled as a multi-threaded library? This is where the error is coming from. It's not originating in Beast or Asio.

---

## Comment 19

> Username: vinniefalco  
> Created at: 2021-11-11 20:33:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-966606794  

Try running the I/O context with only one thread, using `ssl::stream`

---

## Comment 20

> Username: mhassanshafiq  
> Created at: 2021-11-12 07:04:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-966872779  

@vinniefalco @madmongo1 tried running the code with only single thread in io context. Still got the same result.

---

## Comment 21

> Username: madmongo1  
> Created at: 2021-11-12 07:42:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-966890767  

I guess the next step is to isolate the SSL library and test it in isolation. There error you are seeing is originating there.  
Can you confirm:  
- Which SSL library you are linking to  
- Which version  
- How it was installed  
- What is the underlying OS  
etc

---

## Comment 22

> Username: mhassanshafiq  
> Created at: 2021-11-12 08:23:21 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-966912743  

OpenSSL 1.0.2k-fips  26 Jan 2017  
install on `CentOS Linux release 7.9.2009 (Core)` using `yum -y install openssl-devel`

---

## Comment 23

> Username: vinniefalco  
> Created at: 2021-11-12 18:05:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-967313271  

> 26 Jan 2017  
  
That's pretty old...

---

## Comment 24

> Username: stale[bot]  
> Created at: 2022-01-09 03:16:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2168#issuecomment-1008220692  

This issue has been open for a while with no activity, has it been resolved?
