# #247 - Command line option to ignore the site-config.jam [Closed]

> Username: vinniefalco  
> Created at: 2017-10-01 17:27:14 UTC  
> Updated at: 2017-10-27 18:40:03 UTC  
> Closed at: 2017-10-27 18:40:03 UTC  
> Url: https://github.com/boostorg/build/issues/247  

This command line switch is needed to allow package managers to create 100% controlled, deterministic, reproducible builds.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-10-01 17:39:25 UTC  
> Url: https://github.com/boostorg/build/issues/247#issuecomment-333393154  

Attn: @ras0219-msft

---

## Comment 2

> Username: swatanabe  
> Created at: 2017-10-01 17:57:41 UTC  
> Url: https://github.com/boostorg/build/issues/247#issuecomment-333394374  

AMDG  
  
On 10/01/2017 11:27 AM, Vinnie Falco wrote:  
> This command line switch is needed to allow package managers to create 100% controlled, deterministic, reproducible builds.  
>   
>   
  
It's undocumented, but it actually does exist:  
  
--ignore-site-config  
  
Note: the reason I haven't documented this is  
that each config files has a different method  
for disabling it.  It should really be unified.  
  
In Christ,  
Steven Watanabe
