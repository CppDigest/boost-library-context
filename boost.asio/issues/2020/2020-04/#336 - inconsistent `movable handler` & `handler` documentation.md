# #336 - inconsistent `movable handler` & `handler` documentation [Closed]

> Username: jurko-gospodnetic  
> Created at: 2020-04-02 03:04:52 UTC  
> Updated at: 2020-12-30 01:13:43 UTC  
> Closed at: 2020-12-30 01:13:42 UTC  
> Url: https://github.com/boostorg/asio/issues/336  

`Handler` documentation (e.g. https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/reference/Handler.html) says handlers need only be `MoveConstructible`, while the `Movable handler` documentation (e.g. https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/overview/cpp2011/move_handlers.html) says the move construction is an optimization and that:  
  
> Boost.Asio may be able to eliminate all calls to a handler's copy constructor. However, handler types are still required to be copy constructible.   
  
I expect the `Movable handler` documentation is just out of date and should be updated.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:13:41 UTC  
> Url: https://github.com/boostorg/asio/issues/336#issuecomment-752293543  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#755](https://github.com/chriskohlhoff/asio/issues/755).
