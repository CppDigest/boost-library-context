# #94 Fix different signedness comparison on Arm [Closed]

> Username: aladram  
> Created at: 2022-01-14 13:04:09 UTC  
> Updated at: 2022-01-25 17:18:37 UTC  
> Closed at: 2022-01-24 08:25:31 UTC  
> Url: https://github.com/boostorg/property_tree/pull/94  

wchar_t is an unsigned on Arm, therefore a promotion is necessary here to correctly perform the comparison  
  
More information about Armv8 types on https://developer.arm.com/documentation/den0024/a/Porting-to-A64/Data-types

---

## Comment 1

> Username: aladram  
> Created_at: 2022-01-21 17:46:43 UTC  
> Url: https://github.com/boostorg/property_tree/pull/94#issuecomment-1018728656  

@madmongo1 any chance you could take a look at this when you have some time? Thanks!

---

## Comment 2

> Username: madmongo1  
> Created_at: 2022-01-21 17:47:55 UTC  
> Url: https://github.com/boostorg/property_tree/pull/94#issuecomment-1018729393  

Thank you. Will action today.

---
