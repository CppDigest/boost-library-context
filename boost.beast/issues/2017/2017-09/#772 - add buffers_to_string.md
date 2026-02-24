# #772 - add buffers_to_string [Closed]

> Username: vinniefalco  
> Created at: 2017-09-09 19:17:36 UTC  
> Updated at: 2017-09-20 18:13:31 UTC  
> Closed at: 2017-09-20 18:13:31 UTC  
> Url: https://github.com/boostorg/beast/issues/772  

Convert a ConstBufferSequence to a `std::string`. This is very useful and we should have kept it. A lot of tests duplicate the functionality. This can go in `<beast/core/buffer.hpp>` along with some other utilities, perhaps `buffers_range`.
