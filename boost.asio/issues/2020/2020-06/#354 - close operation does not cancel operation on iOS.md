# #354 - close operation does not cancel operation on iOS [Closed]

> Username: ArtBlnd  
> Created at: 2020-06-01 09:11:11 UTC  
> Updated at: 2020-12-30 01:14:25 UTC  
> Closed at: 2020-12-30 01:14:24 UTC  
> Url: https://github.com/boostorg/asio/issues/354  

We are using boost on iOS. seems close operation does not cancel(also cancel explicitly does not work) operations correctly and it causes leaks.

---

## Comment 1

> Username: ArtBlnd  
> Created at: 2020-06-01 09:22:45 UTC  
> Url: https://github.com/boostorg/asio/issues/354#issuecomment-636730172  

I tried with BOOST_ASIO_DISABLE_KQUEUE but still leaks operations. on Android, Windows it works fine with same implementation.

---

## Comment 2

> Username: ArtBlnd  
> Created at: 2020-06-01 10:00:31 UTC  
> Url: https://github.com/boostorg/asio/issues/354#issuecomment-636753656  

note that this bug is hard to reproduce. just it sometimes does not cancel whole operation when its closing.

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 01:14:23 UTC  
> Url: https://github.com/boostorg/asio/issues/354#issuecomment-752293663  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#762](https://github.com/chriskohlhoff/asio/issues/762).
