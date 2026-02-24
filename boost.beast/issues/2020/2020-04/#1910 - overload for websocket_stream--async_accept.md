# #1910 - overload for websocket_stream::async_accept [Closed]

> Username: jpramosi  
> Created at: 2020-04-21 13:26:31 UTC  
> Updated at: 2020-04-24 18:00:53 UTC  
> Closed at: 2020-04-24 18:00:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1910  

I would like to propose the following function signature in beast/websocket/stream.hpp for websocket_stream::async_accept:  
  
```cpp  
    template<  
        class Body, class Allocator,  
        BOOST_BEAST_ASYNC_TPARAM1 AcceptHandler =  
            net::default_completion_token_t<executor_type>  
    >  
    BOOST_BEAST_ASYNC_RESULT1(AcceptHandler)  
    async_accept(  
        http::request<Body,  
            http::basic_fields<Allocator>> const& req,  
        http::response<Body,  
            http::basic_fields<Allocator>> const& res,  
        AcceptHandler&& handler =  
            net::default_completion_token_t<  
                executor_type>{});  
```  
  
It would allow to pass a response which can use the user defined allocator.  
If there's another way to do this, i would be happy to know about.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-04-21 13:30:14 UTC  
> Url: https://github.com/boostorg/beast/issues/1910#issuecomment-617180904  

Hi @reapler,  
  
I'll discuss it with Vinnie.  
  
I understand the general motivation. Do you have a specific motivating use case? This would obviously help in prioritisation.

---

## Comment 2

> Username: jpramosi  
> Created at: 2020-04-21 14:08:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1910#issuecomment-617203915  

Well, it would be a step forward to manage *all* memory in Beast.  
If someone wants to build a high level library depending on Beast and would like to improve the performance and reduce / eliminate memory fragmentation issues at all cost, it would be convenient if the memory used by Beast is manageable.

---

## Comment 3

> Username: jpramosi  
> Created at: 2020-04-21 14:09:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1910#issuecomment-617204350  

i just noticed async_connect would also need this kind of overload for the request

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-04-21 14:30:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1910#issuecomment-617216728  

Are you doing so many handshakes, that your performance measurements indicate this is a bottleneck?

---

## Comment 5

> Username: jpramosi  
> Created at: 2020-04-21 15:19:55 UTC  
> Updated at: 2020-04-21 15:22:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1910#issuecomment-617247073  

No, i don't think this is a bottleneck :)  
The second sentence was more a general statement about the advantages of managed memory.  
If you would like, i can also try to make a pull request with these additional changes on async_accept and async_connect.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-04-21 16:55:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1910#issuecomment-617283871  

These seems like unnecessary complexity.
