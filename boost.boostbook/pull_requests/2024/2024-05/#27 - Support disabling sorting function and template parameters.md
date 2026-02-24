# #27 Support disabling sorting function and template parameters [Merged]

> Username: Lastique  
> Created at: 2024-05-13 00:52:17 UTC  
> Updated at: 2024-05-13 12:36:13 UTC  
> Merged at: 2024-05-13 12:36:09 UTC  
> Closed at: 2024-05-13 12:36:09 UTC  
> Url: https://github.com/boostorg/boostbook/pull/27  

This commit adds a new XSL parameter `boost.sort.params`, which allows users to disable alphabetical sorting of the function and template parameters in detailed descriptions of functions and classes. By default, sorting is enabled, which maintains the previous behavior.  
  
Sorting is disabled when the parameter is set to 0. In this case, parameters are listed in the order they are listed in the BoostBook documentation, which is normally the same as they are listed in the function signature or template preamble.  
  
Before (or after, with `boost.sort.params` set to 1 or default):  
![Screenshot_20240513_034014](https://github.com/boostorg/boostbook/assets/1589747/4198da56-7a26-4749-a173-704840c27280)  
  
After, with `boost.sort.params=0`:  
![Screenshot_20240513_034102](https://github.com/boostorg/boostbook/assets/1589747/15a90bb3-6ca7-4bc3-8e98-631d69609a91)

---
