# #199 - Boost::Python::Numpy library is missing in Windows build [Closed]

> Username: jzmaddock  
> Created at: 2018-05-10 10:59:16 UTC  
> Updated at: 2018-06-20 15:14:08 UTC  
> Closed at: 2018-06-20 15:14:07 UTC  
> Url: https://github.com/boostorg/python/issues/199  

Moved from https://svn.boost.org/trac10/ticket/13219

---

## Comment 1

> Username: ndevenish  
> Created at: 2018-06-20 15:04:15 UTC  
> Url: https://github.com/boostorg/python/issues/199#issuecomment-398784555  

I just had this issue with 1.67 - turns out that the Python installation boost was picking up didn't have `numpy` installed.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2018-06-20 15:06:09 UTC  
> Url: https://github.com/boostorg/python/issues/199#issuecomment-398785179  

@ndevenish , can you confirm that rebuilding with `numpy` installed will actually solve this issue ? That would allow me to close this issue. Thanks !

---

## Comment 3

> Username: ndevenish  
> Created at: 2018-06-20 15:11:46 UTC  
> Url: https://github.com/boostorg/python/issues/199#issuecomment-398787185  

Yes, after installing numpy onto the python boost was looking at, the library got created and installed.  
  
I had to pass `--debug-configuration` to `b2` to actually see the test it was making, which might be useful to anyone having this problem in the future.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2018-06-20 15:14:07 UTC  
> Url: https://github.com/boostorg/python/issues/199#issuecomment-398787976  

Good point.  
Thanks for checking !
