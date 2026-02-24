# #29 - Add compile checks for BBv2 predef_check utilities. [Closed]

> Username: grafikrobot  
> Created at: 2015-07-13 13:53:46 UTC  
> Updated at: 2015-08-27 15:20:18 UTC  
> Closed at: 2015-08-27 15:20:18 UTC  
> Url: https://github.com/boostorg/predef/issues/29  

Having the BBv2 support layer for predef_check use runtime checks is problematic in cross-compile use case as the check program is run in the host-os not the target-os. Change the check to use a compile success/failure to do the BBv2 feature testing to support this use case.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2015-08-27 15:20:18 UTC  
> Url: https://github.com/boostorg/predef/issues/29#issuecomment-135466693  

Implemented some time ago and testing seems OK. So calling it done.
