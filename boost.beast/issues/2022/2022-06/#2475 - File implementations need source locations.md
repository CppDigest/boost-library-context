# #2475 - File implementations need source locations [Closed]

> Username: vinniefalco  
> Created at: 2022-06-26 20:30:26 UTC  
> Updated at: 2022-12-20 05:16:41 UTC  
> Closed at: 2022-12-20 05:16:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2475  

Errors originating in file implementations (e.g. `file_win32`) need source locations.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:30:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2475#issuecomment-1256853442  

That's based on the recent improvements of system::error_Code
