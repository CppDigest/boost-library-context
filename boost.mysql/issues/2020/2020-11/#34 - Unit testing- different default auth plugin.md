# #34 - Unit testing: different default auth plugin [Closed]

> Username: anarthal  
> Created at: 2020-11-29 18:17:30 UTC  
> Updated at: 2025-10-07 15:00:26 UTC  
> Closed at: 2025-10-07 15:00:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/34  

Verify that we handle correctly the case where the default auth plugin selected by the server is:  
- The same as the one we selected.  
- Different than the one we selected.  
- One we do not support (currently unsupported).

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-07 15:00:26 UTC  
> Url: https://github.com/boostorg/mysql/issues/34#issuecomment-3377318108  

Fixed by #469 and #442
