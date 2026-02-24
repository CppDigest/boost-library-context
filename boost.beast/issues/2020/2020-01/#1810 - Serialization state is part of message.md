# #1810 - Serialization state is part of message [Open]

> Username: mika-fischer  
> Created at: 2020-01-20 16:56:42 UTC  
> Updated at: 2022-09-24 05:44:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1810  

I think it would be nicer if the message was const and all the serialization state was in the serializer. Is there any reason why this is not so?  
  
The problem I'm having is this: I'm writing a HTTP client and I want to reuse a connection for subsequent requests. If the request fails (for a limited set of reasons, including that the server closed the connection in the meantime), I need to re-connect and then re-send the request.  
  
Due to the way I structured the method, I only start reading after sending the request (otherwise I might notice the dropped connection earlier).  
  
The issue now is that the error due to dropped connection sometimes occurs only after serializing part of the request body (for instance, a file upload). Then I re-connect, re-create the serializer and re-send the request. The problem with this is that in case of a file upload, the basic_file_body contained in the request does no longer point to the beginning of the file. In the end this leads to an infinite loop, where I wait for `serializer::is_done()` to become true, which never happens, because not enough bytes come out of the file_body, due to the bytes consumed by the first, failed, request.  
  
I don't see a way to reset the state of the body at that point in the code, since I don't know the body type, and even if I did, the only way to reset the file body seems to be to re-open the file, for which I would need to know the filename.  
  
Maybe it would be good if each body type had a reset() function, to reset its state?  
  
If there's a better way to accomplish what I want, I'm all ears...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-01-20 17:40:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1810#issuecomment-576374030  

The message _is_ const, **except** when serializing a `file_body`. This is a design flaw with file bodies. In my opinion you should just avoid that body type. Read or write the file manually in chunks using the `buffer_body` type.

---

## Comment 2

> Username: mika-fischer  
> Created at: 2020-01-21 07:52:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1810#issuecomment-576560081  

Well, that is somewhat disappointing, since we wanted to take advantage of the TransmitFile-support of `file_body` on Windows (and in time, hopefully sendfile on Linux)...  
  
If avoiding `file_body` is recommended, maybe it should be deprecated?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-01-21 15:00:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1810#issuecomment-576720967  

> maybe it should be deprecated?  
  
Well, it will be completely rewritten in the Great HTTP Refactor. There's nothing wrong with using it now, subject to the limitation that it makes the message non-const during serialization.

---

## Comment 4

> Username: mika-fischer  
> Created at: 2020-01-22 08:19:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1810#issuecomment-577060975  

With https://github.com/boostorg/beast/pull/1814 this can be worked around by seeking to the beginning of the file when re-trying the transfer of a file_body.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-01-27 10:45:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1810#issuecomment-578691029  

Shoud we close this issue for now, or leave it open for guidance in TGHR?

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-01-27 13:06:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1810#issuecomment-578737614  

It isn't resovled, so it stays open. I added it to the Project.

---

## Comment 7

> Username: stale[bot]  
> Created at: 2020-02-27 05:33:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1810#issuecomment-591790501  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 8

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:44:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1810#issuecomment-1256872185  

Should we consider an example using an asio::stream_file?
