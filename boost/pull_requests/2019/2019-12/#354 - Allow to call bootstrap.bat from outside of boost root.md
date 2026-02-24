# #354 Allow to call bootstrap.bat from outside of boost root [Closed]

> Username: david-sinuela-pix4d  
> Created at: 2019-12-17 10:54:09 UTC  
> Updated at: 2020-10-12 06:54:15 UTC  
> Closed at: 2020-10-12 06:54:15 UTC  
> Url: https://github.com/boostorg/boost/pull/354  

This is useful to to make sure that builds start from the same state.  
  
The b2 executable is still built within the source folder but it is not a big problem because when bootstrap is run again it gets overwritten.

---
