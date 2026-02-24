# #108 Fixed a lot of typos [Merged]

> Username: denzor200  
> Created at: 2022-09-14 14:02:16 UTC  
> Updated at: 2023-01-18 16:05:32 UTC  
> Merged at: 2023-01-18 16:05:32 UTC  
> Closed at: 2023-01-18 16:05:32 UTC  
> Url: https://github.com/boostorg/pfr/pull/108  



---

## Comment 1

> Username: coveralls  
> Created_at: 2022-09-14 14:17:07 UTC  
> Updated_at: 2022-11-27 11:28:56 UTC  
> Url: https://github.com/boostorg/pfr/pull/108#issuecomment-1246833679  

## Pull Request Test Coverage Report for [Build 3557965427](https://coveralls.io/builds/54589873)  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/54589873/badge)](https://coveralls.io/builds/54589873) |  
| :-- | --: |  
| Change from base [Build 3028143860](https://coveralls.io/builds/52360190): |  0.0% |  
| Covered Lines: | 337 |  
| Relevant Lines: | 337 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Review 2 [Commented]

> Username: denzor200  
> Created_at: 2022-09-14 16:53:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/108#pullrequestreview-1107881663  

📁 include/boost/pfr/io.hpp

```diff
  91 | 
  92 |- /// IO manupulator to read/write \aggregate `value` using its IO stream operators or using \forcedlink{io_fields} if operators are not awailable.
  92 |+ /// IO manipulator to read/write \aggregate `value` using its IO stream operators or using \forcedlink{io_fields} if operators are not awailable.
```

> Username: denzor200  
> Created_at: 2022-09-14 16:51:14 UTC  
> Updated_at: 2022-09-14 16:53:46 UTC  
> Url: https://github.com/boostorg/pfr/pull/108#discussion_r971071826  

We can also fix `awailable` -> `available`


📁 include/boost/pfr/io_fields.hpp

```diff
  24 |- /// Contains IO manupulator \forcedlink{io_fields} to read/write \aggregate `value` field-by-field.
  24 |+ /// Contains IO manipulator \forcedlink{io_fields} to read/write an \aggregate field-by-field.
  25 | ///
```

> Username: denzor200  
> Created_at: 2022-09-14 16:53:07 UTC  
> Updated_at: 2022-09-14 16:53:46 UTC  
> Url: https://github.com/boostorg/pfr/pull/108#discussion_r971073699  

fix `an` -> `a`


---

## Review 3 [Changes requested]

> Username: apolukhin  
> Created_at: 2022-09-24 09:51:45 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/pfr/pull/108#pullrequestreview-1119234040  

📁 doc/pfr.qbk

```diff
 476 | 
 477 | * Static variables are ignored
 478 |- * T must be aggregate initializable without empty base classes
```

> Username: apolukhin  
> Created_at: 2022-09-24 09:47:09 UTC  
> Updated_at: 2022-09-24 09:51:45 UTC  
> Url: https://github.com/boostorg/pfr/pull/108#discussion_r979213791  

If I recall correctly, the old text was precise. Loophole implementation could work with aggregates that have non empty base classes

> Username: denzor200  
> Created_at: 2022-11-27 11:11:01 UTC  
> Url: https://github.com/boostorg/pfr/pull/108#discussion_r1032911135  

I'm sorry for the later answer! Here: https://godbolt.org/z/vWh5zjdqY  
Should we fix it?

---

📁 doc/pfr.qbk

```diff
 475 | The Boost.PFRs reflection has some limitations that depend on a C++ Standard and compiler capabilities:
```

> Username: apolukhin  
> Created_at: 2022-09-24 09:51:34 UTC  
> Updated_at: 2022-09-24 09:51:45 UTC  
> Url: https://github.com/boostorg/pfr/pull/108#discussion_r979214907  

You've accidentally changed file permissions to 755 from 644  
  
Please restore the permissions via ’git chmod -x the-affected-files’

> Username: denzor200  
> Created_at: 2022-11-27 11:17:39 UTC  
> Url: https://github.com/boostorg/pfr/pull/108#discussion_r1032912165  

fixed


---
