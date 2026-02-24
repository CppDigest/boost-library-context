# #1576 - Cleanup CI broken [Closed]

> Username: vinniefalco  
> Created at: 2019-04-18 20:32:47 UTC  
> Updated at: 2019-04-19 01:09:48 UTC  
> Closed at: 2019-04-19 01:09:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1576  

These changes failed, and had to be reverted in v252:  
https://github.com/boostorg/beast/pull/1558  
  
The problem is that the command to copy the beast repository working copy has to be different for Appveyor than Travis (Appveyor has to use XCOPY with slightly different parameters). I couldn't figure out how to fix it properly so I rolled it back in order to apply other fixes.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-04-19 01:09:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1576#issuecomment-484736802  

Fixed in v252
