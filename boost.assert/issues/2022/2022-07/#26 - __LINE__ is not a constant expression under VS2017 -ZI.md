# #26 - __LINE__ is not a constant expression under VS2017 /ZI [Closed]

> Username: pdimov  
> Created at: 2022-07-20 17:00:07 UTC  
> Updated at: 2022-07-22 07:13:38 UTC  
> Closed at: 2022-07-22 07:13:38 UTC  
> Url: https://github.com/boostorg/assert/issues/26  

https://godbolt.org/z/Pboz77Ysn, as reported in https://github.com/chriskohlhoff/asio/issues/1089.

---

## Comment 1

> Username: pdimov  
> Created at: 2022-07-22 07:13:38 UTC  
> Url: https://github.com/boostorg/assert/issues/26#issuecomment-1192260048  

Fixed by https://github.com/boostorg/assert/commit/3d6e55bddf6f12dbb2bf0ef634f1b145990854d3.
