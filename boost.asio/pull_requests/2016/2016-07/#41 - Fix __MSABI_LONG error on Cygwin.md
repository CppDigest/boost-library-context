# #41 Fix __MSABI_LONG error on Cygwin [Closed]

> Username: thtrummer  
> Created at: 2016-07-20 11:00:46 UTC  
> Updated at: 2018-10-20 07:43:10 UTC  
> Closed at: 2018-10-20 07:43:10 UTC  
> Url: https://github.com/boostorg/asio/pull/41  

Including 'winerror.h' directly requires an additional header file (with  
the correct solution probably being to include 'windows.h' or using the  
error definitions from Boost.WinApi)

---
