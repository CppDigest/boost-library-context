# #251 - Don't make a copy in consuming_buffers [Closed]

> Username: vinniefalco  
> Created at: 2017-02-04 12:38:58 UTC  
> Updated at: 2017-02-18 00:06:36 UTC  
> Closed at: 2017-02-18 00:06:36 UTC  
> Url: https://github.com/boostorg/beast/issues/251  

Instead of storing a copy of the buffer sequence, `consuming_buffers` can just cache the `end` iterator.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-02-04 17:55:50 UTC  
> Updated at: 2017-02-04 17:57:15 UTC  
> Url: https://github.com/boostorg/beast/issues/251#issuecomment-277462977  

I'm having second thoughts now, this introduces lifetime issues. Asio code always makes a copy. Well written code won't use expensive to copy buffer sequences (like `std::vector`).  
  
For example `basic_streambuf::const_buffers_type` is merely a wrapper around a pointer, its as cheap to copy as possible for a buffer sequence type.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-02-18 00:06:36 UTC  
> Url: https://github.com/boostorg/beast/issues/251#issuecomment-280800535  

I already made the decision that classes like this should make a copy of the buffer sequence (but not the underlying memory). So we should not do this.
