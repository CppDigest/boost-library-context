# #1605 - Message classes and HTTP/2.0 [Closed]

> Username: brjoha  
> Created at: 2019-05-09 19:11:41 UTC  
> Updated at: 2019-06-15 21:37:02 UTC  
> Closed at: 2019-06-15 21:37:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1605  

Just wanted to let the author(s) know that we're successfully using the Beast message classes in an HTTP/2.0 proxy server implementation based on the nghttp2 library.  There are a couple of trivial warts, like when printing the messages...  
  
     HTTP/2.0        <<< request line isn't applicable, nor correct  
    :method: GET  
    :path: /  
    :scheme: http  
    :authority: 127.0.0.1:8080  
    user-agent: curl/7.64.1  
    accept: */*  
  
    HTTP/2.0 200 OK        <<< status line isn't applicable, nor correct  
    :status: 404  
  
...but otherwise it works.  There is also unavoidable header and payload copying that occurs, due to nghttp2 itself.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-05-09 20:20:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1605#issuecomment-491051292  

I am very glad to hear this, feedback about how the library is being used is helpful when I make improvements! If there is anything you'd like me to change or improve, or add, this is a good time and place to mention it. I am adding this issue to the "Great HTTP Refactor" project, which I will begin in 2020 (the plan is to rewrite/refactor all the HTTP code to fix design flaws).

---

## Comment 2

> Username: brjoha  
> Created at: 2019-05-09 20:33:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1605#issuecomment-491055788  

There are some properties unique to whether the message is HTTP/1.x or HTTP/2.0.  The output logic is one example as illustrated above.  Another would be enum and string support for HTTP/2.0 (pseudo) headers.  HTTP/2.0 doesn't require payload preparation either.  All fairly trivial, fortunately.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-05-09 20:35:47 UTC  
> Updated at: 2019-05-09 20:36:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1605#issuecomment-491056392  

And there's no such thing as chunked in HTTP/2? The `operator<<` support is really just there for debugging and diagnostics. It is not intended to be heavy-duty serialization.

---

## Comment 4

> Username: brjoha  
> Created at: 2019-05-09 21:12:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1605#issuecomment-491068161  

Correct on chunked...  
  
"HTTP/2 uses DATA frames to carry message payloads.  The "chunked" transfer encoding defined in Section 4.1 of [RFC7230] MUST NOT be used in HTTP/2."  
  
Yes, we're only using operator << in debug logs, that's why I said these are trivial nits.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2019-06-08 21:32:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1605#issuecomment-500165970  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 6

> Username: stale[bot]  
> Created at: 2019-06-15 21:37:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1605#issuecomment-502401177  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
