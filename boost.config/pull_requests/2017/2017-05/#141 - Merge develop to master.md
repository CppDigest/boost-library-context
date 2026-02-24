# #141 Merge develop to master [Merged]

> Username: jzmaddock  
> Created at: 2017-05-25 12:29:18 UTC  
> Updated at: 2017-05-25 19:07:00 UTC  
> Merged at: 2017-05-25 16:58:08 UTC  
> Closed at: 2017-05-25 16:58:08 UTC  
> Url: https://github.com/boostorg/config/pull/141  



---

## Review 1 [Commented]

> Username: apolukhin  
> Created_at: 2017-05-25 19:03:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/141#pullrequestreview-40364518  

📁 appveyor.yml

```diff
  18 |+   matrix:
  19 |+     - ARGS: --toolset=msvc-9.0  address-model=32
  20 |+     - ARGS: --toolset=msvc-10.0 address-model=32
```

> Username: apolukhin  
> Created_at: 2017-05-25 19:03:20 UTC  
> Url: https://github.com/boostorg/config/pull/141#discussion_r118560107  

Appveyor also supports MinGW. See for example https://github.com/boostorg/variant/blob/develop/test/appveyor.yml#L31  
  
Just adding my 2 cents.


---

## Comment 2

> Username: jzmaddock  
> Created_at: 2017-05-25 19:07:00 UTC  
> Url: https://github.com/boostorg/config/pull/141#issuecomment-304096331  

> Appveyor also supports MinGW. See for example   
> https://github.com/boostorg/variant/blob/develop/test/appveyor.yml#L31  
>  
> Just adding my 2 cents.  
>  
  
I know... I just haven't got msvc completely sorted yet :(  One thing at   
a time...  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---
