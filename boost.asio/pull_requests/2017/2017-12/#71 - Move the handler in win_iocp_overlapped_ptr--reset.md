# #71 Move the handler in win_iocp_overlapped_ptr::reset [Closed]

> Username: vinniefalco  
> Created at: 2017-12-02 21:42:31 UTC  
> Updated at: 2017-12-02 21:46:43 UTC  
> Closed at: 2017-12-02 21:46:43 UTC  
> Url: https://github.com/boostorg/asio/pull/71  

This fixes the problem where move-only handlers are not supported with `boost::asio::windows::overlapped_ptr`

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-12-02 21:46:43 UTC  
> Url: https://github.com/boostorg/asio/pull/71#issuecomment-348722497  

Nope, this is wrong - disregard

---
