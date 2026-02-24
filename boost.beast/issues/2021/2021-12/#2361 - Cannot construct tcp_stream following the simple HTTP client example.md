# #2361 - Cannot construct tcp_stream following the simple HTTP client example [Closed]

> Username: Jordan181  
> Created at: 2021-12-13 13:01:18 UTC  
> Updated at: 2022-06-16 14:28:24 UTC  
> Closed at: 2022-06-16 14:28:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2361  

`net::io_context ioc;  
tcp::resolver resolver(ioc);  
beast::tcp_stream stream(ioc);  
`  
This does not compile due to no matching constructor for tcp_stream.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-12-13 13:40:50 UTC  
> Updated at: 2021-12-13 13:40:58 UTC  
> Url: https://github.com/boostorg/beast/issues/2361#issuecomment-992486806  

It compliles on godbolt.  
  
https://godbolt.org/z/j59Gco7sq  
  
Are you in a position to share the program that is not compiling?

---

## Comment 2

> Username: Jordan181  
> Created at: 2021-12-13 15:08:39 UTC  
> Url: https://github.com/boostorg/beast/issues/2361#issuecomment-992573531  

Noticed that godbolt is using gcc, tried compiling with gcc myself and it works. I was using MSVC before.

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-12-13 15:49:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2361#issuecomment-992612593  

It's compiling on godbolt for MSVC too.  
  
https://godbolt.org/z/YeWEovxhW  
  
Are you able to confirm for me that the code here is as you would expect?

---

## Comment 4

> Username: Jordan181  
> Created at: 2021-12-13 15:57:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2361#issuecomment-992619254  

Ah okay. For some reason Visual Studio identifies it as an error and gives a red squiggle in the editor but I can actually compile it with MSVC (which I didn't try before hand as I have never come across a situation like this). Is this a VS Intellisense bug then do you think?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-12-13 16:59:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2361#issuecomment-992678466  

Intellisense uses a different compiler than what is used to compile the program, sometimes it does not reflect actual compilation results.
