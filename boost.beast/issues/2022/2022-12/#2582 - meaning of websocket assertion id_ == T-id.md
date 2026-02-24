# #2582 - meaning of websocket assertion id_ == T:id [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:31:47 UTC  
> Updated at: 2022-12-13 15:50:46 UTC  
> Closed at: 2022-12-13 15:50:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2582  

### Discussed in https://github.com/boostorg/beast/discussions/2321  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **prokopowicz** September 30, 2021</sup>  
lhttps://github.com/boostorg/beast/blob/b15a5ff0e47e72ba3d4711d2514bc65749d036ae/include/boost/beast/websocket/detail/soft_mutex.hpp#L75  
  
I get this assertion in my unit tests; it seems to be timing dependent.  What error case is this assertion guarding against?</div>

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:50:46 UTC  
> Url: https://github.com/boostorg/beast/issues/2582#issuecomment-1348865806  

This usuall means that a given op (e.g. async_read) gets invoked again before the first initation  completes.
