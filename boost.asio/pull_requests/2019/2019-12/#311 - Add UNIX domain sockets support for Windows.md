# #311 Add UNIX domain sockets support for Windows [Closed]

> Username: huangqinjin  
> Created at: 2019-12-23 17:54:17 UTC  
> Updated at: 2020-12-30 15:24:44 UTC  
> Closed at: 2020-12-30 01:33:13 UTC  
> Url: https://github.com/boostorg/asio/pull/311  

closes #119 .

---

## Comment 1

> Username: petrutlucian94  
> Created_at: 2020-10-29 08:19:20 UTC  
> Url: https://github.com/boostorg/asio/pull/311#issuecomment-718467448  

This patch does indeed allow using unix sockets on Windows. We rely on it for Ceph Windows support but we have to cherry-pick it: https://github.com/ceph/ceph/blob/master/win32_deps_build.sh#L213-L246

---

## Comment 2

> Username: chriskohlhoff  
> Created_at: 2020-12-30 01:33:13 UTC  
> Url: https://github.com/boostorg/asio/pull/311#issuecomment-752296687  

Fixed in asio 1.18.1 / boost 1.75.

---
