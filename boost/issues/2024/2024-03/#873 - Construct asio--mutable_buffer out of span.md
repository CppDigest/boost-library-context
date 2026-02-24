# #873 - Construct asio::mutable_buffer out of span [Closed]

> Username: ujos  
> Created at: 2024-03-26 10:29:54 UTC  
> Updated at: 2024-03-26 10:33:45 UTC  
> Closed at: 2024-03-26 10:33:44 UTC  
> Url: https://github.com/boostorg/boost/issues/873  

Could you please add an option to create an instance of the `boost::asio::mutable_buffer` class out of `std::span` / `boost::span`?  
  
It is easy to manipulate with the buffer via the span interface. You can do `subspan` without any reallocations and prepare data ptr/size in a natural way. What is missing is a `asio::mutable_buffer` constructor that accepts the span.

---

## Comment 1

> Username: ujos  
> Created at: 2024-03-26 10:33:44 UTC  
> Url: https://github.com/boostorg/boost/issues/873#issuecomment-2020069888  

Please ignore this ticket. It seems users can use `boost::asio::buffer()` function instead of explicitly create an instance of the `mutable_buffer` class
