# #457 Fix #456 resource leak in `awaitable::operator=` [Open]

> Username: ddvamp  
> Created at: 2025-09-03 05:51:53 UTC  
> Updated at: 2025-09-03 06:08:39 UTC  
> Url: https://github.com/boostorg/asio/pull/457  

`awaitable::operator=` did not destroy the existing frame

---
