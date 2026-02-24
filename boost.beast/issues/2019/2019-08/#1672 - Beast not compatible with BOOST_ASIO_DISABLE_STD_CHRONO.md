# #1672 - Beast not compatible with BOOST_ASIO_DISABLE_STD_CHRONO [Closed]

> Username: gjasny  
> Created at: 2019-08-01 13:43:14 UTC  
> Updated at: 2019-09-01 09:15:36 UTC  
> Closed at: 2019-09-01 09:15:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1672  

### Version of Beast  
  
Boost 1.70 / 248  
  
### Steps necessary to reproduce the problem  
  
When defining the BOOST_ASIO_DISABLE_STD_CHRONO toggle I get the following error:  
  
```  
/home/gregorj/.cache/cmake_build_system/ExternalLibs/boost/1.70.0_1559918081/boost/beast/websocket/impl/stream_impl.hpp:416:21: error: no viable conversion from 'boost::beast::websocket::stream_base::duration' (aka 'duration<long, ratio<1, 1000000000> >') to 'const boost::asio::basic_waitable_timer<boost::chrono::steady_clock, boost::asio::wait_traits<boost::chrono::steady_clock>, boost::asio::executor>::duration' (aka 'const duration<long, ratio<1L, 1000000000L> >')  
                    timeout_opt.handshake_timeout);  
```  
  
This is most probably due to a std and boost chrono type mismatch.  
  
Could you maybe define the beast `duration` and `timepoint` based on `boost::asio::chono`?  
  
Thanks,  
Gregor

---

## Comment 1

> Username: djarek  
> Created at: 2019-08-01 13:53:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1672#issuecomment-517297724  

Why are you using `BOOST_ASIO_DISABLE_STD_CHRONO`? Beast requires (fairly complete) C++11.

---

## Comment 2

> Username: gjasny  
> Created at: 2019-08-01 14:12:35 UTC  
> Url: https://github.com/boostorg/beast/issues/1672#issuecomment-517305422  

I think it originates from the broken std::chrono implementation with vs2013. Right now we're at vs2017.  
  
I'm unsure if https://developercommunity.visualstudio.com/content/problem/61684/stdthis-threadsleep-for-depends-on-system-time.html also applies to other std::chrono parts on VisualStudio. So developers are a little bit hesitant to switch back to std::chono.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-08-01 15:41:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1672#issuecomment-517342391  

> vs2013  
  
Beast does not support VS2013

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-08-31 16:02:17 UTC  
> Url: https://github.com/boostorg/beast/issues/1672#issuecomment-526841963  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: gjasny  
> Created at: 2019-09-01 09:15:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1672#issuecomment-526901683  

I removed `BOOST_ASIO_DISABLE_STD_CHRONO` and nothing bad has happened :)
