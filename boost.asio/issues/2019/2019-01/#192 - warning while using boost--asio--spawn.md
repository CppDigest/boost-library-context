# #192 - warning while using boost::asio::spawn [Closed]

> Username: savagecm  
> Created at: 2019-01-09 02:52:52 UTC  
> Updated at: 2020-12-30 00:59:20 UTC  
> Closed at: 2020-12-30 00:59:18 UTC  
> Url: https://github.com/boostorg/asio/issues/192  

boost::asio::spawn is based on boost::coroutine, but boost::coroutine is now deprecated.   
Any plan  to get spawn update with  Boost.Coroutine2?

---

## Comment 1

> Username: djarek  
> Created at: 2019-01-09 20:30:45 UTC  
> Url: https://github.com/boostorg/asio/issues/192#issuecomment-452845462  

#55

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-01-22 12:42:10 UTC  
> Url: https://github.com/boostorg/asio/issues/192#issuecomment-456385807  

The warning should be gone now:  https://github.com/boostorg/coroutine/commit/f226f9ba278fd531f9e723e7965e63c0c6d6fd10

---

## Comment 3

> Username: AndrewAMD  
> Created at: 2019-01-22 16:46:45 UTC  
> Url: https://github.com/boostorg/asio/issues/192#issuecomment-456472002  

> The warning should be gone now: [boostorg/coroutine@f226f9b](https://github.com/boostorg/coroutine/commit/f226f9ba278fd531f9e723e7965e63c0c6d6fd10)  
  
If boost coroutine is deprecated, why was this warning removed?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-01-22 17:21:38 UTC  
> Updated at: 2019-01-22 17:28:50 UTC  
> Url: https://github.com/boostorg/asio/issues/192#issuecomment-456485402  

> If boost coroutine is deprecated, why was this warning removed?  
  
Because users of Asio cannot do anything about the warning, so it is just noise. Until Asio is no longer dependent on Boost.Coroutine, the warning is not actionable.

---

## Comment 5

> Username: ghost  
> Created at: 2020-12-30 00:59:17 UTC  
> Url: https://github.com/boostorg/asio/issues/192#issuecomment-752291054  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#667](https://github.com/chriskohlhoff/asio/issues/667).
