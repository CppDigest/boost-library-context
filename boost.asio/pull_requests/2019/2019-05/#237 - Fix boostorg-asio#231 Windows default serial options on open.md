# #237 Fix boostorg/asio#231 Windows default serial options on open [Closed]

> Username: jrctrimble  
> Created at: 2019-05-15 16:12:41 UTC  
> Updated at: 2020-12-30 01:54:34 UTC  
> Closed at: 2020-12-30 01:54:34 UTC  
> Url: https://github.com/boostorg/asio/pull/237  

Sets the defaults for a serial port explicitly on open on the Windows platform to match other platform functionality. Without this, they're inherited from the last use of the port.

---

## Comment 1

> Username: chriskohlhoff  
> Created_at: 2020-12-30 01:54:34 UTC  
> Url: https://github.com/boostorg/asio/pull/237#issuecomment-752299693  

Fixed in asio 1.14.1 / boost 1.71.

---
