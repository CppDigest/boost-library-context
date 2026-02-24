# #521 - Documentation issues [Closed]

> Username: harrishancock  
> Created at: 2017-06-21 05:24:53 UTC  
> Updated at: 2017-06-22 17:41:12 UTC  
> Closed at: 2017-06-22 17:41:12 UTC  
> Url: https://github.com/boostorg/beast/issues/521  

Little nits on current master branch:  
  
- `boost::asio_handler_type` is misspelled [here](https://github.com/vinniefalco/Beast/blob/master/include/beast/core/async_result.hpp#L35).  
  
- [`handler_type`](http://vinniefalco.github.io/beast/beast/ref/beast__handler_type.html) and [`async_return_type`](http://vinniefalco.github.io/beast/beast/ref/beast__async_return_type.html) both duplicate [`async_result`](http://vinniefalco.github.io/beast/beast/ref/beast__async_result.html)'s content.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 05:58:36 UTC  
> Url: https://github.com/boostorg/beast/issues/521#issuecomment-309971617  

1. It says `boost::asio::handler_type`, isn't that correct? http://www.boost.org/doc/libs/1_64_0/boost/asio/handler_type.hpp  
  
2. Nothing I can really do about that, its an artifact of doxygen's handling of template aliases.

---

## Comment 2

> Username: harrishancock  
> Created at: 2017-06-21 06:00:06 UTC  
> Url: https://github.com/boostorg/beast/issues/521#issuecomment-309971834  

1. Click the link I posted in the first bullet.  
  
2. Gotcha, c'est la vie.

---

## Comment 3

> Username: harrishancock  
> Created at: 2017-06-21 06:00:59 UTC  
> Url: https://github.com/boostorg/beast/issues/521#issuecomment-309971973  

I'm sorry, the misspelling is `boost::asio_async_result`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-21 06:08:39 UTC  
> Url: https://github.com/boostorg/beast/issues/521#issuecomment-309973170  

willfix

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-21 06:12:25 UTC  
> Url: https://github.com/boostorg/beast/issues/521#issuecomment-309973747  

@harrishancock This is a big ask, and if you are otherwise preoccupied I will of course understand; *but*, the formal review is 9 days out. If you want to go through every public interface (or even some of them) and anything which you think is questionable or weak I will try to address it if I agree. I've been doing this in the last two days, but of course I am biased and not really objective. On the other hand, it does look pretty good (what did you think of the `prepare_payload` change, and the change to `Fields` requirements?)

---

## Comment 6

> Username: harrishancock  
> Created at: 2017-06-21 06:24:29 UTC  
> Url: https://github.com/boostorg/beast/issues/521#issuecomment-309975668  

I'll do what I can! My schedule is severely squeezed right now, though.  
  
To be honest, I have only used the websocket and core parts of Beast so far, still no HTTP (though that might change this week), so I don't have educated opinions on the changes you mentioned yet.  
  
I will have feedback for you on the composed op tutorial very shortly, btw. Will send by email tonight.
