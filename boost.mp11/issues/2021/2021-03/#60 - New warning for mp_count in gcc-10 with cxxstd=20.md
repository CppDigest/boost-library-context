# #60 - New warning for mp_count in gcc-10 with cxxstd=20 [Closed]

> Username: HDembinski  
> Created at: 2021-03-24 15:51:34 UTC  
> Updated at: 2021-03-25 16:31:30 UTC  
> Closed at: 2021-03-25 16:31:30 UTC  
> Url: https://github.com/boostorg/mp11/issues/60  

```  
../../boost/mp11/detail/mp_count.hpp:54:31: error: comparison of unsigned expression in ‘< 0’ is always false [-Werror=type-limits]  
   54 |     for( std::size_t i = 0; i < sizeof...(T); ++i )  
      |                             ~~^~~~~~~~~~~~~~  
cc1plus: all warnings being treated as errors  
```  
https://github.com/boostorg/histogram/pull/317/checks?check_run_id=2185335442#step:5:198

---

## Comment 1

> Username: HDembinski  
> Created at: 2021-03-24 15:52:30 UTC  
> Url: https://github.com/boostorg/mp11/issues/60#issuecomment-805942747  

These warnings in constexpr contexts are a bit useless sometimes.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-03-24 16:00:37 UTC  
> Url: https://github.com/boostorg/mp11/issues/60#issuecomment-805949646  

Entirely useless, but what can you do. Thanks. I now need to figure out why I'm not getting this warning on CI (or locally).  
  
I was being too clever by adding a `false` in front to avoid specializing for the zero case, but apparently this trick won't work.

---

## Comment 3

> Username: pdimov  
> Created at: 2021-03-24 16:05:17 UTC  
> Updated at: 2021-03-24 16:05:28 UTC  
> Url: https://github.com/boostorg/mp11/issues/60#issuecomment-805953906  

Ah, I'm missing `<warnings>extra`. Let's see what else will break.

---

## Comment 4

> Username: pdimov  
> Created at: 2021-03-24 19:30:55 UTC  
> Url: https://github.com/boostorg/mp11/issues/60#issuecomment-806098912  

Should be fixed now.
