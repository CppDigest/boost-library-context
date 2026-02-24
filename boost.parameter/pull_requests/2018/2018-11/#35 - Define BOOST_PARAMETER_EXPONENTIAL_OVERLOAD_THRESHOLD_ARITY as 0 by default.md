# #35  Define BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY as 0 by default [Merged]

> Username: CromwellEnage  
> Created at: 2018-11-20 13:54:38 UTC  
> Updated at: 2018-11-21 06:51:42 UTC  
> Merged at: 2018-11-20 18:07:46 UTC  
> Closed at: 2018-11-20 18:07:46 UTC  
> Url: https://github.com/boostorg/parameter/pull/35  

User library tests have been reporting numerous failures due to internal compiler errors that occur when perfect forwarding is unsupported.  Setting BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY to zero eliminates these failures.  By doing this in Boost.Parameter, we relieve user code of the burden of doing this in their end.  The trade-off is that our own tests need to define BOOST_PARAMETER_EXPONENTIAL_OVERLOAD_THRESHOLD_ARITY explicitly in order to take advantage of the benefits of not having to wrap boost::ref() or std::ref() around non-const lvalues.  As this was always a C++03 workaround, the change should be worth the trade-off.  
  
On an unrelated note, Travis Cl just deprecated "sudo: false".  See <https://blog.travis-ci.com/2018-10-04-combining-linux-infrastructures>.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-11-20 17:20:48 UTC  
> Url: https://github.com/boostorg/parameter/pull/35#issuecomment-440356632  

I will merge this when the CI tests pass. However looking at the explanations for the new Parameter configuration macros in the reference I think your explanation needs to be a little bit better. Here are my suggestions:  
  
1) Add BOOST_PARAMETER_DISABLE_PERFECT_FORWARDING as a configuration macro.  
2) When referring to other macros which are not a part of Parameter itself you need to mention specifically in what libraries those macros are in, else the end-user will not understand to what you are referring.   
3) If any of the non-Parameter macros can be altered to affect the Parameter configuration you need to mention this.  
  
These suggestions, if you choose to heed them which is totally up to you, should be another separate PR, since this PR looks good to me.

---
