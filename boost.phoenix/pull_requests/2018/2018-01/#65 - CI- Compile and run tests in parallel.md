# #65 CI: Compile and run tests in parallel [Merged]

> Username: Kojoley  
> Created at: 2018-01-26 19:13:54 UTC  
> Updated at: 2018-01-27 16:13:10 UTC  
> Merged at: 2018-01-27 07:59:43 UTC  
> Closed at: 2018-01-27 07:59:43 UTC  
> Url: https://github.com/boostorg/phoenix/pull/65  

Travis Linux containers and Appveyor VMs has two CPU cores. There is no reason not to run tests in parallel.

---

## Comment 1

> Username: djowel  
> Created_at: 2018-01-26 21:12:56 UTC  
> Url: https://github.com/boostorg/phoenix/pull/65#issuecomment-360905688  

👍

---

## Comment 2

> Username: Flast  
> Created_at: 2018-01-27 02:39:41 UTC  
> Url: https://github.com/boostorg/phoenix/pull/65#issuecomment-360953666  

Because I was afraid of massive slashing cased by memory footprint, I didn't set the flag but it seemed needless anxiety.

---

## Comment 3

> Username: Flast  
> Created_at: 2018-01-27 07:56:03 UTC  
> Url: https://github.com/boostorg/phoenix/pull/65#issuecomment-360968009  

OK, I googled and got `sysctl -n hw.ncpu` for OS X. I fix it and merge this PR locally.

---
