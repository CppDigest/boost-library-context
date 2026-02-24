# #689 - array<2> optimization [Closed]

> Username: vinniefalco  
> Created at: 2017-07-28 02:51:28 UTC  
> Updated at: 2017-07-29 07:27:26 UTC  
> Closed at: 2017-07-29 07:27:26 UTC  
> Url: https://github.com/boostorg/beast/issues/689  

`static_buffer_base` should use `std::array<const_buffer, 2>` and `std::array<mutable_buffer, 2>` for its buffer types to take advantage of this significant optimization:  
https://github.com/chriskohlhoff/asio/blob/master/asio/include/asio/detail/buffer_sequence_adapter.hpp#L421
