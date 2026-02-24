# #174 - buffers_iterator depends on value_type [Closed]

> Username: vinniefalco  
> Created at: 2018-11-30 06:42:51 UTC  
> Updated at: 2020-12-30 00:57:49 UTC  
> Closed at: 2020-12-30 00:57:48 UTC  
> Url: https://github.com/boostorg/asio/issues/174  

The implementation of `buffers_iterator` assumes the type `BufferSequence::value_type` exists. However, this nested type was removed in Boost 1.67. This should probably be using `iterator_traits` instead.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:57:47 UTC  
> Url: https://github.com/boostorg/asio/issues/174#issuecomment-752290740  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#657](https://github.com/chriskohlhoff/asio/issues/657).
