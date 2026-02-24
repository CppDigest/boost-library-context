# #251 Fix BOOST_NO_FENV_H on cygwin (it exists since 2010...) [Merged]

> Username: jeking3  
> Created at: 2018-11-01 19:37:33 UTC  
> Updated at: 2018-11-02 13:45:32 UTC  
> Merged at: 2018-11-02 13:45:32 UTC  
> Closed at: 2018-11-02 13:45:32 UTC  
> Url: https://github.com/boostorg/config/pull/251  

I tested this in a private build on AppVeyor CI against numeric/interval where the problem originally surfaced.  This build job failed without the change:  
  
https://ci.appveyor.com/project/jeking3/interval/builds/19983035/job/9glx1bs1r28a421l  
  
Ref:  
  
https://github.com/Alexpux/Cygwin/commit/0f81b5d4bcaa5c840031796d94df9ba6d6fae4d0#diff-447ff859802166057451911061326417R390  
  
Thanks.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-11-02 12:16:34 UTC  
> Url: https://github.com/boostorg/config/pull/251#issuecomment-435359978  

This is blocking progress on getting CI into numeric/interval.  Please merge... thanks.

---
