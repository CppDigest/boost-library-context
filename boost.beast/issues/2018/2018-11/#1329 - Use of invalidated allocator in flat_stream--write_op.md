# #1329 - Use of invalidated allocator in flat_stream::write_op [Closed]

> Username: djarek  
> Created at: 2018-11-28 12:17:13 UTC  
> Updated at: 2019-02-09 18:58:48 UTC  
> Closed at: 2019-02-09 18:21:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1329  

If flat_stream::write_op is dropped, the Handler is destroyed before the allocated buffer (owned by `unique_ptr`) which may result in the use of an invalidated Allocator.  
  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/_experimental/core/impl/flat_stream.ipp#L60

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-03 22:19:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1329#issuecomment-443892179  

Isn't fixing this as easy as changing the order of the data members?

---

## Comment 2

> Username: djarek  
> Created at: 2018-12-04 09:20:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1329#issuecomment-444028647  

Yes, it would fix this particular issue, but:  
- I don't think the Handler's associated allocator is appropriate for use as an allocator for large buffers.  
- You still can end up with weird allocator states if the handler throws during move construction and the memory resource is owned by the handler.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-12-04 13:37:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1329#issuecomment-444101967  

We need these questions answered, possibly in a paper.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-02-07 16:34:42 UTC  
> Url: https://github.com/boostorg/beast/issues/1329#issuecomment-461501146  

Is there any sane way to handle an exception when moving a handler?

---

## Comment 5

> Username: djarek  
> Created at: 2019-02-09 01:18:44 UTC  
> Url: https://github.com/boostorg/beast/issues/1329#issuecomment-461998883  

Assuming the handler owns the memory resource, you have no way of knowing whether the memory resource is valid or not if the move throws.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-02-09 01:31:16 UTC  
> Url: https://github.com/boostorg/beast/issues/1329#issuecomment-462000262  

The new implementation no longer uses the associated allocator, have you had a look?

---

## Comment 7

> Username: djarek  
> Created at: 2019-02-09 18:21:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1329#issuecomment-462067161  

Yep, looks fine, too bad there's no way to fix the issue in ssl stream's inability to do proper scatter/gather.

---

## Comment 8

> Username: vinniefalco  
> Created at: 2019-02-09 18:58:41 UTC  
> Updated at: 2019-02-09 18:58:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1329#issuecomment-462070128  

> too bad there's no way to fix the issue in ssl stream's inability to do proper scatter/gather.  
  
OpenSSL has a scatter/gather API, asio just needs to use it.
