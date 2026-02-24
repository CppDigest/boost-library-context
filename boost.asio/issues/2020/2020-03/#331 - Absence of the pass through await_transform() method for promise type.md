# #331 - Absence of the pass through await_transform() method for promise type [Closed]

> Username: vignatyuk  
> Created at: 2020-03-18 20:25:18 UTC  
> Updated at: 2020-12-30 01:13:31 UTC  
> Closed at: 2020-12-30 01:13:30 UTC  
> Url: https://github.com/boostorg/asio/issues/331  

Absence of the pass through await_transform() method for an unknown awaitable types blocks usage of the user defined coroutine friendly completion handlers.  
  
I propose to change the only available pass through await_transform() method from:  
  
  template <typename Executor>  
  template <typename T>  
  auto boost::asio::awaitable_frame_base<Executor>::await_transform(awaitable<T, Executor> a) const  
  {  
    return a;  
  }  
  
to the  
  
  template <typename Executor>  
  template <typename T, typename = std::enable_if_t<is_awaitable_v<T>>>  
  decltype(auto) boost::asio::awaitable_frame_base<Executor>::await_transform(T&& a) const noexcept  
  {  
    return std::forward<T>(a);  
  }  
  
Second default argument through std::enable_if_t<> may be removed if new await_transform() method does not break any other overloads. There is no standard is_awaitable_v check, so it should be defined somewhere, though it's easy.to implement.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:13:29 UTC  
> Url: https://github.com/boostorg/asio/issues/331#issuecomment-752293503  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#752](https://github.com/chriskohlhoff/asio/issues/752).
