# #137 Fix ICU libs detection when ICU resources are in a separate directory [Merged]

> Username: volo-zyko  
> Created at: 2021-06-09 18:53:20 UTC  
> Updated at: 2021-06-12 13:21:40 UTC  
> Merged at: 2021-06-12 13:21:39 UTC  
> Closed at: 2021-06-12 13:21:39 UTC  
> Url: https://github.com/boostorg/regex/pull/137  

ICU supports build setups when its data is loaded at runtime from a separate directory. In this case ICU is not usable until `u_setDataDirectory` is called with a proper directory path.

---
