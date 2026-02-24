# #6 Initial constexpr support [Closed]

> Username: gnzlbg  
> Created at: 2014-05-30 14:40:22 UTC  
> Updated at: 2014-08-06 09:10:38 UTC  
> Closed at: 2014-08-06 09:10:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/6  

This allows to use, e.g., boost::fusion::fold within constexpr functions.  
  
The BOOST_CONSTEXPR macro is used to declare functions constexpr.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-08-05 20:44:55 UTC  
> Url: https://github.com/boostorg/fusion/pull/6#issuecomment-51256986  

I think it looks fine.

---

## Comment 2

> Username: djowel  
> Created_at: 2014-08-05 20:45:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/6#issuecomment-51257085  

the problem is that we should not be messing with 'master'. instead, this should target 'develop'.

---

## Comment 3

> Username: gnzlbg  
> Created_at: 2014-08-06 09:10:38 UTC  
> Url: https://github.com/boostorg/fusion/pull/6#issuecomment-51310656  

@djowel i've submitted a pull-request for develop. Sorry about that!

---
