# #2989 - async_read not returning with -DNDEBUG cpp flag on arm64 (but normal on x86 cpu) [Closed]

> Username: voidiii  
> Created at: 2025-02-26 04:50:38 UTC  
> Updated at: 2025-08-29 23:25:22 UTC  
> Closed at: 2025-08-29 23:25:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2989  

I have been working an http server built with beast and until recently I have decided to remove the -DNDEBUG cpp flag during compile and noticed on arm64 machine the async_read() function is not returning immediately as it shoud be. Has anyone else encountered similiar problems? I am sure the only difference is the -DNDEBUG cpp flag.

---

## Comment 1

> Username: voidiii  
> Created at: 2025-02-26 11:20:46 UTC  
> Updated at: 2025-02-26 11:21:38 UTC  
> Url: https://github.com/boostorg/beast/issues/2989#issuecomment-2684676001  

I did some digging and found it was   
  
```  
 template <typename Initiation,  
      BOOST_ASIO_COMPLETION_HANDLER_FOR(Signatures...) RawCompletionToken,  
      typename... Args>  
  static return_type initiate(  
      BOOST_ASIO_MOVE_ARG(Initiation) initiation,  
      BOOST_ASIO_MOVE_ARG(RawCompletionToken) token,  
      BOOST_ASIO_MOVE_ARG(Args)... args)  
  {  
    BOOST_ASIO_MOVE_CAST(Initiation)(initiation)(  
        BOOST_ASIO_MOVE_CAST(RawCompletionToken)(token),  
        BOOST_ASIO_MOVE_CAST(Args)(args)...);  
  }  
```  
  
that hangs up

---

## Comment 2

> Username: ashtum  
> Created at: 2025-02-27 10:06:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2989#issuecomment-2687473073  

Does this happen only in your code, or can you reproduce it using the existing examples?

---

## Comment 3

> Username: ashtum  
> Created at: 2025-04-27 16:26:34 UTC  
> Updated at: 2025-04-27 16:26:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2989#issuecomment-2833535642  

@voidiii, Any update on this? What's your platform? Could you provide a minimal reproducible example?

---

## Comment 4

> Username: voidiii  
> Created at: 2025-08-29 23:25:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2989#issuecomment-3238692672  

Sry it was a bug I ran into during work and I no longer work there. I dont think it is beast's problem though, I bulit a smaller testing server and the function works fine.
