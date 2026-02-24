# #2516 - [Question] Control the download speed/upload speed on SSL stream [Closed]

> Username: laper32  
> Created at: 2022-09-07 17:34:45 UTC  
> Updated at: 2024-11-26 17:29:41 UTC  
> Closed at: 2024-11-26 17:11:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2516  

I want to upload a file to a server, but I want to control my upload rate.  
Since we are now using SSL, however, I have no idea how to control the rate under this case...  
  
The `tcp_stream` have function to control the rate directly, so what should I do in order to control the rate with the `ssl_stream`? cuz I've searched the code about `ssl_stream`, that I didn't find anything about controlling the rate...  
  
Perhaps it may be a little mess since it is not well organized.

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-09-07 18:01:51 UTC  
> Updated at: 2022-09-07 18:04:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2516#issuecomment-1239709998  

You can either intersperse each invocation of `async_read_some` with a delay based on how much data was read since the last read, or you can write your own `rate_limited_stream` type that implements the AsyncReadStream concept.  
  
The first method is the easiest.  
  
Are you using HTTP or Websockets? Callbacks or coroutines? If you need more guidance I can write a little demo.

---

## Comment 2

> Username: laper32  
> Created at: 2022-09-08 01:58:30 UTC  
> Updated at: 2022-09-08 03:52:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2516#issuecomment-1240127249  

> Are you using HTTP or Websockets?  
  
In fact I'm now using both (my library is required to support both of them)  
  
> Callbacks or coroutines?  
  
I'm now using coroutines  
  
> If you need more guidance I can write a little demo.  
  
Oh really? I just thought how can I implement it.  
Thanks for your kind

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:12:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2516#issuecomment-1256851156  

@madmongo1 How'd you go about this? Drop in a delay between calls to async_read_some? I wonder if rate-limited ops could be a nice demo of async_compose

---

## Comment 4

> Username: madmongo1  
> Created at: 2022-09-24 04:17:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2516#issuecomment-1256851827  

Couple of ways, depending big on whether you’re looking to have a hard cap on bit rate or want to have a constant average bit rate.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2024-11-26 17:29:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2516#issuecomment-2501543018  

you can do this using beast's `tcp_stream`
