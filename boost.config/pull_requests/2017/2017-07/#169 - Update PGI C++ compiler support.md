# #169 Update PGI C++ compiler support [Merged]

> Username: dkolsen-pgi  
> Created at: 2017-07-26 20:12:36 UTC  
> Updated at: 2017-08-01 17:11:08 UTC  
> Merged at: 2017-08-01 17:11:08 UTC  
> Closed at: 2017-08-01 17:11:08 UTC  
> Url: https://github.com/boostorg/config/pull/169  

Remove an old PGI-specific workaround for intptr_t.  The workaround is no longer necessary and now causes compilation errors.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-07-27 11:50:16 UTC  
> Url: https://github.com/boostorg/config/pull/169#issuecomment-318339795  

Is it possible to make the workaround PGI version checked, rather than removed altogether?

---

## Comment 2

> Username: dkolsen-pgi  
> Created_at: 2017-07-27 17:01:36 UTC  
> Url: https://github.com/boostorg/config/pull/169#issuecomment-318424037  

It would be possible to have a version check, but it would not be useful.  The versions of the PGI compiler that needed this workaround are old enough that we (PGI) do not know of any customers still actively those versions.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-07-27 17:24:08 UTC  
> Url: https://github.com/boostorg/config/pull/169#issuecomment-318429742  

On 27/07/2017 18:01, David Olsen wrote:  
>  
> It would be possible to have a version check, but it would not be   
> useful. The versions of the PGI compiler that needed this workaround   
> are old enough that we (PGI) do not know of any customers still   
> actively those versions.  
>  
  
Fair enough!  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
