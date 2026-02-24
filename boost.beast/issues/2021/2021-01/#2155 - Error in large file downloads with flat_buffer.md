# #2155 - Error in large file downloads with flat_buffer [Closed]

> Username: bulbaasd  
> Created at: 2021-01-27 16:44:10 UTC  
> Updated at: 2021-01-28 11:38:34 UTC  
> Closed at: 2021-01-28 11:38:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2155  

Hello,   
I tried this example ( https://www.boost.org/doc/libs/1_75_0/libs/beast/example/http/client/sync/http_client_sync.cpp) to download a large file ( ~ 400+MB ).  
  
This is what I noticed:  
I get a partial message error when trying to use a flat_buffer.  
I get rid of the error when trying to use a multi_buffer with maximum body limit.  
Would like to know, why I observe this behaviour. Is it a bug with flat_buffer?  
Note: I would not doubt the server implementation, as the behaviour is noticed only with asio, and not curl/wget.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-27 16:49:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2155#issuecomment-768419401  

Can you share the url so I can replicate the test?

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-01-27 17:08:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2155#issuecomment-768432315  

I have run this test locally, using a node http server and a 500MB file.  
The program ran to completion.  
Are you able to share any more information?

---

## Comment 3

> Username: bulbaasd  
> Created at: 2021-01-28 11:38:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2155#issuecomment-768995182  

I'm sorry. I was working with a very old version of boost.  
Everything seems to work fine with the newest version.
