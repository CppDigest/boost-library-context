# #148 - Coverity static analysis defects (Trac ticket #13331) [Closed]

> Username: mloskot  
> Created at: 2018-09-21 08:51:41 UTC  
> Updated at: 2020-12-30 00:53:22 UTC  
> Closed at: 2020-12-30 00:53:21 UTC  
> Url: https://github.com/boostorg/asio/issues/148  

(copied from https://svn.boost.org/trac10/ticket/13331, only Asio's parts from the attached report)  
  
Boost 1.57.0  
  
A static analysis tool called Coverity found medium and high defects in the boost source code. See attached file for defect type, defect category, filename and line number of defect.   
  
  
Defect   Type | Defect Category | Line Number | Filename  
-- | -- | -- | --  
Unchecked   return value from library | Error handling issues | 473 | /boost/include/boost/asio/detail/impl/epoll_reactor.ipp  
Unchecked   return value from library | Error handling issues | 84 | /boost/include/boost/asio/detail/impl/eventfd_select_interrupter.ipp  
Unchecked   return value from library | Error handling issues | 581 | /boost/include/boost/asio/detail/impl/signal_set_service.ipp  
Unchecked   return value from library | Error handling issues | 500 | /boost/include/boost/asio/detail/impl/epoll_reactor.ipp  
Unitialized   pointer field | Unitialized members | 54,55,58,59,60,62,606 | /boost/include/boost/asio/detail/impl/epoll_reactor.ipp  
Out-of-bounds   access | Memory-corruptions | 385 | /boost/include/boost/asio/impl/read_until.hpp  
Out-of-bounds   access | Memory-corruptions | 577 | /boost/include/boost/asio/impl/read_until.hpp  
Uninitialized   scalar filed | Uninitialized members | 50 | /boost/include/boost/asio/detail/timer_queue.hpp  
Unchecked   return value | Error handling issues | 46 | /boost/include/boost/asio/detail/posix_mutex.hpp

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-11 15:09:21 UTC  
> Url: https://github.com/boostorg/asio/issues/148#issuecomment-428992352  

Boost version 1.57.0? Or do you mean 1.67.0?

---

## Comment 2

> Username: mloskot  
> Created at: 2018-10-12 09:52:37 UTC  
> Url: https://github.com/boostorg/asio/issues/148#issuecomment-429271322  

I meant what I copied from the Trac ticket, without doing any verification though  
  
![image](https://user-images.githubusercontent.com/80741/46862502-44e08500-ce15-11e8-9462-8c7c667fa1c9.png)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-10-12 16:37:15 UTC  
> Url: https://github.com/boostorg/asio/issues/148#issuecomment-429386283  

Every Coverity anaysis on my own code that people have run for me, only turned up false positives. Your mileage may vary.

---

## Comment 4

> Username: mloskot  
> Created at: 2018-10-12 19:24:54 UTC  
> Updated at: 2018-10-12 19:31:19 UTC  
> Url: https://github.com/boostorg/asio/issues/148#issuecomment-429435301  

@vinniefalco I am not the author of this analysis. This was just me cleaning up backlog of Trac tickets, split-moving this one to GitHub. Please, take it as FYI and do with it what you desire.

---

## Comment 5

> Username: ghost  
> Created at: 2020-12-30 00:53:20 UTC  
> Url: https://github.com/boostorg/asio/issues/148#issuecomment-752289931  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#640](https://github.com/chriskohlhoff/asio/issues/640).
