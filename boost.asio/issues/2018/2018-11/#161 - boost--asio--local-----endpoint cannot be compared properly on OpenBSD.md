# #161 - boost::asio::local::*::endpoint cannot be compared properly on OpenBSD [Closed]

> Username: nomis  
> Created at: 2018-11-03 10:21:27 UTC  
> Updated at: 2020-12-30 00:56:43 UTC  
> Closed at: 2020-12-30 00:56:42 UTC  
> Url: https://github.com/boostorg/asio/issues/161  

On Linux the `sun_path` is always returned with a single `\0` at the end (by truncating `socklen` of `sockaddr_un` to match). Asio removes the final `\0` character if one is present.  
  
On OpenBSD (6.4) the `socklen` of `sockaddr_un` is always `sizeof(sockaddr_un)`. Boost.Asio (1.66.0) removes the final `\0` character if one is present but it leaves all the other `\0` characters. This causes comparison of two endpoints to fail if one of them has the original constructed path and the other was set by `async_receive_from` because the strings are not equal.  
  
All of the trailing `\0` characters should be removed from the path if it does not start with a `\0` character. (It looks like there's currently an issue with comparison of abstract addresses too because they should not have a trailing `\0` character removed.)

---

## Comment 1

> Username: nomis  
> Created at: 2018-11-03 10:24:38 UTC  
> Url: https://github.com/boostorg/asio/issues/161#issuecomment-435576716  

A workaround for this is to update the endpoint being used for comparison after binding it:  
```  
socket_.bind(ep_);  
ep_ = socket_.local_endpoint();  
```

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:56:41 UTC  
> Url: https://github.com/boostorg/asio/issues/161#issuecomment-752290564  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#649](https://github.com/chriskohlhoff/asio/issues/649).
