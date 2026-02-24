# #49 Fixed cross-compilation of tests [Closed]

> Username: NiklasAngare  
> Created at: 2017-02-11 23:37:06 UTC  
> Updated at: 2020-12-30 02:15:25 UTC  
> Closed at: 2020-12-30 02:15:25 UTC  
> Url: https://github.com/boostorg/asio/pull/49  

Removed the use of the "os" feature as it reflects the host and not the target when cross-compiling.  
  
All platform libs are declared unconditionally since I don't believe it's possible to use target-os in module scope. Declaring unused and unavailable libs does no harm.  
  
Replaced \<os\> with \<target-os\> so that the correct options for the target are selected when cross-compiling. It works when not cross-compiling as well.  
  
This is needed and is working for my regression test runner for QNX 6.6.0 which is cross-compiling from Linux to QNX.  
  
I have checked the validity of the values provided for target-os against the feature definition in  build/src/tools/builtin.jam.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 02:15:25 UTC  
> Url: https://github.com/boostorg/asio/pull/49#issuecomment-752302976  

Fixed in boost 1.69.

---
