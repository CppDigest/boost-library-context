# #907 - Use iterator wrapper in detail::buffers_range [Closed]

> Username: vinniefalco  
> Created at: 2017-11-26 14:09:11 UTC  
> Updated at: 2017-11-29 04:09:47 UTC  
> Closed at: 2017-11-29 04:09:47 UTC  
> Url: https://github.com/boostorg/beast/issues/907  

`detail::buffers_range` needs an iterator wrapper to convert the value type to const or mutable buffer so that range-for loops work for all sequences
