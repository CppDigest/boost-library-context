# #263 - asio::async_read_until/asio::read_until is significantly slower on Windows 10 [Closed]

> Username: LunaNeumeister  
> Created at: 2019-07-22 20:39:59 UTC  
> Updated at: 2020-12-30 01:07:16 UTC  
> Closed at: 2020-12-30 01:07:15 UTC  
> Url: https://github.com/boostorg/asio/issues/263  

Reading from a serial device until reaching a given delimiter is significantly slower on Windows 7 vs Windows 10.  
  
Boost Versions: 1.64.0 - 1.7.0  
  
Windows 7: ~7 milliseconds to read until the delimiter  
Windows 10: ~81 milliseconds to read until the delimiter

---

## Comment 1

> Username: djarek  
> Created at: 2019-07-23 13:25:18 UTC  
> Url: https://github.com/boostorg/asio/issues/263#issuecomment-514208567  

Could it be an issue in the implementation in Windows?

---

## Comment 2

> Username: LunaNeumeister  
> Created at: 2019-07-23 17:59:13 UTC  
> Url: https://github.com/boostorg/asio/issues/263#issuecomment-514318294  

Entirely possible, have yet to step through or profile asio::read_until to see if there is an obvious issue with its Windows implementation.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 01:07:14 UTC  
> Url: https://github.com/boostorg/asio/issues/263#issuecomment-752292581  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#706](https://github.com/chriskohlhoff/asio/issues/706).
