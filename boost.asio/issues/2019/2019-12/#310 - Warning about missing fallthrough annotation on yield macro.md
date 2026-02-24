# #310 - Warning about missing fallthrough annotation on yield macro [Closed]

> Username: jonesmz  
> Created at: 2019-12-19 07:54:49 UTC  
> Updated at: 2020-12-30 01:12:10 UTC  
> Closed at: 2020-12-30 01:12:09 UTC  
> Url: https://github.com/boostorg/asio/issues/310  

> /usr/include/boost/asio/yield.hpp:18:16: note: expanded from macro 'yield'  
> \# define yield BOOST_ASIO_CORO_YIELD  
>                ^  
> /usr/include/boost/asio/coroutine.hpp:326:32: note: expanded from macro 'BOOST_ASIO_CORO_YIELD'  
> \# define BOOST_ASIO_CORO_YIELD BOOST_ASIO_CORO_YIELD_IMPL(__LINE__)  
>                                ^  
> /usr/include/boost/asio/coroutine.hpp:311:37: note: expanded from macro 'BOOST_ASIO_CORO_YIELD_IMPL'  
>             else /* fall-through */ case 0:  
>                                     ^

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:12:08 UTC  
> Url: https://github.com/boostorg/asio/issues/310#issuecomment-752293312  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#737](https://github.com/chriskohlhoff/asio/issues/737).
