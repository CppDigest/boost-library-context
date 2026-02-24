# #52 Add drone debugging variable DRONE_STAGE_MACHINE [Merged]

> Username: sdarwin  
> Created at: 2024-04-17 17:40:01 UTC  
> Updated at: 2024-04-17 18:10:56 UTC  
> Merged at: 2024-04-17 18:10:56 UTC  
> Closed at: 2024-04-17 18:10:56 UTC  
> Url: https://github.com/boostorg/endian/pull/52  

Since the jsonnet method doesn't call functions.star, please add this DRONE_STAGE_MACHINE variable to any projects, as shown.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-17 17:43:32 UTC  
> Url: https://github.com/boostorg/endian/pull/52#issuecomment-2061864962  

`echo $env:DRONE_STAGE_MACHINE` doesn't look correct to me - this is PowerShell syntax. Should probably be `echo %DRONE_STAGE_MACHINE%`?

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-04-17 17:44:22 UTC  
> Url: https://github.com/boostorg/endian/pull/52#issuecomment-2061866283  

Yeah:  
```  
C:\drone\src>echo $env:DRONE_STAGE_MACHINE   
$env:DRONE_STAGE_MACHINE  
```

---

## Comment 3

> Username: sdarwin  
> Created_at: 2024-04-17 18:06:56 UTC  
> Url: https://github.com/boostorg/endian/pull/52#issuecomment-2061910987  

You're right! I copied that from an existing script, which was Powershell, instead of DOS.    
Updated.

---
