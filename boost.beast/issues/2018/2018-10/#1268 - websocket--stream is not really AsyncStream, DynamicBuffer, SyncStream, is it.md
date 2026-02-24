# #1268 - websocket::stream is not really AsyncStream, DynamicBuffer, SyncStream, is it? [Closed]

> Username: reddwarf69  
> Created at: 2018-10-15 10:42:32 UTC  
> Updated at: 2018-11-28 02:09:41 UTC  
> Closed at: 2018-11-28 02:09:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1268  

According to the bottom of https://www.boost.org/doc/libs/master/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream.html `websocket::stream` is `AsyncStream`, `DynamicBuffer` and `SyncStream`.  
  
I may be missing something obvious. But how can it be a `DynamicBuffer`?  
Then it certainly is `AsyncReadStream`/`SyncReadStream`. But since `write_some`/`async_write_some` have the initial `bool fin` parameter they don't meet the requirements of `AsyncWriteStream` and `SyncWriteStream`, do they? And without `AsyncWriteStream`/`SyncWriteStream` they are not `AsyncStream`/`SyncStream`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-15 11:53:30 UTC  
> Updated at: 2018-10-15 11:53:57 UTC  
> Url: https://github.com/boostorg/beast/issues/1268#issuecomment-429822323  

"Concepts" just means the concepts that you should be familiar with to use the class. It does not mean that it models those concepts.

---

## Comment 2

> Username: reddwarf69  
> Created at: 2018-10-15 11:58:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1268#issuecomment-429823901  

Oh, in such a case feel free to close the issue. Unless... do you think somebody else may misread that part of the documentation?

---

## Comment 3

> Username: djarek  
> Created at: 2018-10-15 12:49:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1268#issuecomment-429840288  

@RedDwarf69 websockets deal in frames, while AsyncStreams, as the name suggests, deal in octet streams. You can easily write an adapter that enables stream like operation for a websocket stream, should you need it (e.g. if you were to create a TCP tunnel that works over websocket).

---

## Comment 4

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1268#issuecomment-441859441  

This issue has been open for a while with no activity, has it been resolved?
