# #310 subfeatures are expanded by feature.add-defaults [Closed]

> Username: grisumbras  
> Created at: 2018-05-16 20:09:01 UTC  
> Updated at: 2021-10-02 22:08:15 UTC  
> Closed at: 2018-05-22 18:30:25 UTC  
> Url: https://github.com/boostorg/build/pull/310  

Fixes #308 .

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-05-16 20:34:39 UTC  
> Url: https://github.com/boostorg/build/pull/310#issuecomment-389656448  

AMDG  
  
On 05/16/2018 02:09 PM, Dmitry wrote:  
> Fixes #308 .  
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/build/pull/310  
>   
> -- Commit Summary --  
>   
>   * subfeatures are expanded by feature.add-defaults  
>   
> -- File Changes --  
>   
>     M src/build/feature.jam (4)  
>   
> -- Patch Links --  
>   
> https://github.com/boostorg/build/pull/310.patch  
> https://github.com/boostorg/build/pull/310.diff  
>   
  
Okay, I see what's happening now, but this patch  
is still not quite right.  It breaks the case of  
applying the default of a composite feature.  I  
think $(more) needs to be in *both* worklist and  
to-expand.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: grisumbras  
> Created_at: 2018-05-16 20:43:42 UTC  
> Url: https://github.com/boostorg/build/pull/310#issuecomment-389659139  

Ok, I updated the patch.

---
