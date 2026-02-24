# #1831 - basic_flat_buffer::shrink_to_fit should not throw [Closed]

> Username: vinniefalco  
> Created at: 2020-01-30 21:05:11 UTC  
> Updated at: 2020-02-05 14:40:53 UTC  
> Closed at: 2020-02-05 14:40:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1831  

it should eat the exception, like this:  
https://github.com/vinniefalco/json/blob/2b8ac041fce779644b94c0680f0fdda589be8eac/include/boost/json/detail/impl/string_impl.ipp#L182
