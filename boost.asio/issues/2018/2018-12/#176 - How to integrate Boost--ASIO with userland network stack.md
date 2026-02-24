# #176 - How to integrate Boost::ASIO with userland network stack? [Closed]

> Username: ScottYueqiang  
> Created at: 2018-12-07 06:54:53 UTC  
> Updated at: 2019-07-01 17:26:30 UTC  
> Closed at: 2019-07-01 06:08:52 UTC  
> Url: https://github.com/boostorg/asio/issues/176  

Is there a way to integrate Boost::ASIO with a userland network stack such as F-stack?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-11 15:42:05 UTC  
> Url: https://github.com/boostorg/asio/issues/176#issuecomment-446248264  

Yes

---

## Comment 2

> Username: ScottYueqiang  
> Created at: 2018-12-19 07:45:40 UTC  
> Url: https://github.com/boostorg/asio/issues/176#issuecomment-448499934  

Could you show me some ways? In addition, I need SSL programming. Do I also need to replace the posix API in native SSL with f-stack's posix API

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-07-01 17:26:30 UTC  
> Url: https://github.com/boostorg/asio/issues/176#issuecomment-507354598  

> Could you show me some ways?  
  
No.  
  
> Do I also need to replace the posix API in native SSL with f-stack's posix API  
  
I have no idea
