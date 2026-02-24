# #1011 - asynchronous file operations [Closed]

> Username: cedral  
> Created at: 2018-02-06 22:45:16 UTC  
> Updated at: 2018-02-27 18:07:46 UTC  
> Closed at: 2018-02-27 18:07:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1011  

The file operations are synchronous which is undesirable on an ASIO IO thread. Files opened against network shares can block the io thread for significant amounts of time. I can handle this one of 2 ways I can just use your parser and serialization and do my own IO or I can take a stab at asynchronous versions of reader::put and writer::get with type traits that tell it which way to behave. Obviously my asynchronous version would only exist on windows as that is the only platform ASIO is capable of doing truly asynchronous file operations on. I think that will require more up front work so I don't want to do it unless you are interested. Is that something you might by interested in incorporating if I made it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-07 00:09:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1011#issuecomment-363610088  

The Beast Windows implementation already uses `::TransmitFile`, and it uses the `WSAOVERLAPPED` in asynchronous mode if you use async beast calls. So if you're using `beast::http::file_body` you will automatically get efficient Windows I/O using either synchronous or asynchronous calls. See:  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/file_body_win32.ipp#L432  
  
An earlier iteration of beast had support for asynchronous and coroutine-based "reader" and "writer" types but this proved to be too cumbersome and didn't cover some important use-cases. I think that this is best left to the application layer. Beast provides the building blocks, so it should be possible for you to roll your own solution. For example you could use `buffer_body` with `async_write` and refill the buffer yourself in your own stream algorithm which has awareness of I/O dispatching to other threads.

---

## Comment 2

> Username: cedral  
> Created at: 2018-02-07 13:43:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1011#issuecomment-363772492  

I saw the TransmitFile stuff. However I can't find a call to ReadFile that uses an overlapped pointer. Am I missing something?

---

## Comment 3

> Username: cedral  
> Created at: 2018-02-07 13:45:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1011#issuecomment-363773084  

And yes, I can roll my own. I totally see that. And that is what I will do. Thanks for getting back to me so quickly.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-02-07 15:17:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1011#issuecomment-363801048  

`::TransmitFile` does the read so the application doesn't have to

---

## Comment 5

> Username: cedral  
> Created at: 2018-02-09 22:33:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1011#issuecomment-364588933  

Sure, I got mixed up I was looking at ReadFile as the opposite of TransmitFile. WriteFile would be the opposite. But I also don't see any WriteFile calls with an overlapped pointer.   
Just to to be clear I am finding that buffer_body works great so at this point my question is just informational.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-02-09 22:41:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1011#issuecomment-364590470  

There's no asynchronous file reads in Beast, just the optimized writes using `TransmitFile`. It would be difficult to implement that cross-platform, and beyond the scope of the library.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-02-21 21:27:51 UTC  
> Url: https://github.com/boostorg/beast/issues/1011#issuecomment-367478345  

Has this issue been resolved?

---

## Comment 8

> Username: cedral  
> Created at: 2018-02-27 18:07:46 UTC  
> Url: https://github.com/boostorg/beast/issues/1011#issuecomment-368971491  

Yeah, I'm good. Thanks.
