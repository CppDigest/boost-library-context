# #410 [hotfix] Use correct definition for Boost ASIO when running on Windows [Closed]

> Username: uilianries  
> Created at: 2024-10-03 10:42:16 UTC  
> Updated at: 2024-10-24 23:02:58 UTC  
> Closed at: 2024-10-24 23:02:58 UTC  
> Url: https://github.com/boostorg/process/pull/410  

Hello!  
  
The Boost.Asio project defines the macro `BOOST_ASIO_WINDOWS`, but not `ASIO_WINDOWS` as expected in this project. Reference:  
  
https://github.com/boostorg/asio/blob/boost-1.86.0/include/boost/asio/detail/config.hpp#L713

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2024-10-03 14:51:27 UTC  
> Url: https://github.com/boostorg/process/pull/410#issuecomment-2391626051  

That's for standalone mode - what's going wrong?

---
