# #36 - Unit testing: TLS not available [Closed]

> Username: anarthal  
> Created at: 2020-11-29 18:19:05 UTC  
> Updated at: 2025-10-07 14:59:42 UTC  
> Closed at: 2025-10-07 14:59:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/36  

Verify that we handle correctly the case where TLS is not available and we pass in ssl_mode::enable.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-07 14:59:42 UTC  
> Url: https://github.com/boostorg/mysql/issues/36#issuecomment-3377314909  

Fixed by #442 and #469
