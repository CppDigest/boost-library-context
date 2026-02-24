# #241 - warning: comparing floating point with == or != is unsafe [-Wfloat-equal] [Closed]

> Username: jhasse  
> Created at: 2019-12-11 13:49:27 UTC  
> Updated at: 2020-01-07 06:43:47 UTC  
> Closed at: 2020-01-07 06:43:09 UTC  
> Url: https://github.com/boostorg/test/issues/241  

When using `BOOST_CHECK_CLOSE` I'm getting the following warning:  
  
```  
boost/test/tools/floating_point_comparison.hpp:182:13: warning: comparing floating point with == or != is unsafe [-Wfloat-equal]  
    if( (f1 == static_cast<FPT>(0)) ||  
         ~~ ^  ~~~~~~~~~~~~~~~~~~~  
```  
  
Is there a way to silence this without me deactivating the warning completely?

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-12-14 11:51:22 UTC  
> Url: https://github.com/boostorg/test/issues/241#issuecomment-565710204  

Hi,  
  
Thanks for the report, I've made a fix in the branch `topic/GH-241-floating-point-strict-comparison-warning`. Do you think you can give a try?

---

## Comment 2

> Username: jhasse  
> Created at: 2019-12-16 09:02:30 UTC  
> Url: https://github.com/boostorg/test/issues/241#issuecomment-565968180  

Works! :)

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2020-01-07 06:43:09 UTC  
> Url: https://github.com/boostorg/test/issues/241#issuecomment-571458311  

Merged to master
