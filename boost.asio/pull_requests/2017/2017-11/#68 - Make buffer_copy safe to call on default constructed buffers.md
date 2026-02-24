# #68 Make buffer_copy safe to call on default constructed buffers [Closed]

> Username: djarek  
> Created at: 2017-11-26 01:39:00 UTC  
> Updated at: 2017-12-02 07:29:21 UTC  
> Closed at: 2017-12-02 07:29:21 UTC  
> Url: https://github.com/boostorg/asio/pull/68  

Previously a call to memcpy() with a null pointer could occur, which resulted in UB.  
This is an alternative to #67.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2017-12-02 07:29:21 UTC  
> Url: https://github.com/boostorg/asio/pull/68#issuecomment-348674712  

Fixed in develop.

---
