# #72 Small flat maps [Closed]

> Username: tobias-loew  
> Created at: 2018-05-12 21:51:19 UTC  
> Updated at: 2018-05-12 22:09:33 UTC  
> Closed at: 2018-05-12 22:09:33 UTC  
> Url: https://github.com/boostorg/container/pull/72  

This is an extension to allow specifying the underlying container of small_flat_[map|set] and small_flat_multi[map|set].  
The interface of the flat containers defaults to boost::container::vector but can be overwritten. Additionally there are template aliases for small_flat containers.  
  
One application of small_flat_map is e.g. to simulate sparse vectors that usually have only a very small number of elements but potentially can grow very large.

---

## Comment 1

> Username: tobias-loew  
> Created_at: 2018-05-12 22:09:32 UTC  
> Url: https://github.com/boostorg/container/pull/72#issuecomment-388586789  

I started on an old version - I'll do it again

---
