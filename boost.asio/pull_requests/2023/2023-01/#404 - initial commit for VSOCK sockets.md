# #404 initial commit for VSOCK sockets [Open]

> Username: olk  
> Created at: 2023-01-23 18:10:43 UTC  
> Updated at: 2023-06-12 20:50:07 UTC  
> Url: https://github.com/boostorg/asio/pull/404  

This commit introduces support for VSOCK sockets on Linux.  
  
TODO:  
- documentation  
- support for Windows (address family AF_HYPERV, protocol HV_PROTOCOL_RAW)  
  
Note:  
- SOCK_DGRAM currently not fully implemented in Linux kernel 6.2.1

---

## Comment 1

> Username: lyuxiaosu  
> Created_at: 2023-06-12 20:50:07 UTC  
> Url: https://github.com/boostorg/asio/pull/404#issuecomment-1588076033  

Hi olk, thanks for the submission. It seems it doesn't support async_connect(), is it possible to support it? Thanks very much.

---
