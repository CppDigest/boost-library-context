# #1757 - Way to compress message selectively [Closed]

> Username: qyangdu  
> Created at: 2019-11-06 03:29:56 UTC  
> Updated at: 2025-07-08 09:32:28 UTC  
> Closed at: 2025-07-08 09:32:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1757  

When use permessage deflate, compresion works better for large message than small message. Could Beast support a message size threshold parameter to enable compression for large messages only?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-11-06 03:43:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1757#issuecomment-550129546  

I agree, there should be a way to turn compression on or off for each message.

---

## Comment 2

> Username: jcmonnin  
> Created at: 2019-11-06 08:01:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1757#issuecomment-550192341  

I think the "on off for each message" that @vinniefalco describes covers that, but here is my use case:  
Most the peers are in the same subnet over a gigabit connection, and compression slows down the transfer noticeably for them. There are a few remote hosts where I would like to enable compression. I would like to be able to turn compression on/off depending in the peer address.   
A message size threshold as initially suggested is not flexible enough.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-11-06 12:41:10 UTC  
> Updated at: 2019-11-06 12:46:58 UTC  
> Url: https://github.com/boostorg/beast/issues/1757#issuecomment-550292194  

>  I would like to be able to turn compression on/off depending in the peer address.  
  
You can do that now, fill out a `permessage_deflate` and call `stream::set_option` on it. Do this before the handshake.

---

## Comment 4

> Username: jcmonnin  
> Created at: 2019-11-06 20:41:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1757#issuecomment-550491627  

Thanks. That looks straightforward. I could have figured that out by thinking before writing...

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:53:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1757#issuecomment-1256874007  

Is this still unresolved?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-09-25 00:31:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1757#issuecomment-1257090560  

I believe we did add the per-message compression flag, can we please check?

---

## Comment 7

> Username: qyangdu  
> Created at: 2022-11-16 16:13:31 UTC  
> Updated at: 2022-11-16 16:14:26 UTC  
> Url: https://github.com/boostorg/beast/issues/1757#issuecomment-1317272340  

The current per-message compression flag is a stream-level setting.  
For better performance, the requested feature is to ask compressing messages selectively, e.g. to compress message with size > 500 bytes only.

---

## Comment 8

> Username: ashtum  
> Created at: 2025-07-08 09:32:28 UTC  
> Url: https://github.com/boostorg/beast/issues/1757#issuecomment-3048108252  

Addressed in https://github.com/boostorg/beast/commit/bd69638e9d45fccbfdbb5eb0fae8c6cd4140a196.
