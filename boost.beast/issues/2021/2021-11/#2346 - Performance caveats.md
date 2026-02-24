# #2346 - Performance caveats? [Closed]

> Username: brjoha  
> Created at: 2021-11-20 15:48:51 UTC  
> Updated at: 2021-11-20 23:40:28 UTC  
> Closed at: 2021-11-20 23:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2346  

I came across #1893 by chance, learning that the default executors for asio::basic_stream_socket and beast::basic_stream come with a performance penalty.  
  
I didn't see that documented for these classes, and am wondering if there are any others performance caveats to be aware of?  
  
edit: Per comments in #1998, is the polymorphic executor a performance consideration anymore?

---

## Comment 1

> Username: brjoha  
> Created at: 2021-11-20 23:40:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2346#issuecomment-974727172  

Discussion is ongoing in #1893, so closing here.
