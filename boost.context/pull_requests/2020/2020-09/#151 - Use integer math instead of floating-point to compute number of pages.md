# #151 Use integer math instead of floating-point to compute number of pages [Merged]

> Username: lowsfer  
> Created at: 2020-09-08 08:49:50 UTC  
> Updated at: 2021-03-01 06:39:56 UTC  
> Merged at: 2021-03-01 06:39:51 UTC  
> Closed at: 2021-03-01 06:39:51 UTC  
> Url: https://github.com/boostorg/context/pull/151  

The previous math can go wrong if size_ cannot be exactly expressed with fp32.

---

## Comment 1

> Username: lowsfer  
> Created_at: 2020-09-10 09:52:13 UTC  
> Url: https://github.com/boostorg/context/pull/151#issuecomment-690122700  

The error message for the failed test is not caused by this change. Looks like an infra issue:  
  
> The following packages have unmet dependencies:  
>   
>  clang-5.0 : Depends: libjsoncpp0 (>= 0.6.0~rc2) but it is not installable  
>   
> E: Unable to correct problems, you have held broken packages.  
> apt-get.diagnostics  
>   
> apt-get install failed  
>   
> The command "sudo -E apt-get -yq --no-install-suggests --no-install-recommends $(travis_apt_get_options) install clang-5.0 libstdc++-7-dev" failed and exited with 100 during .

---

## Comment 2

> Username: olk  
> Created_at: 2021-03-01 06:39:56 UTC  
> Url: https://github.com/boostorg/context/pull/151#issuecomment-787691652  

ty

---
