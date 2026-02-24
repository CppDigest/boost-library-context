# #112 - -Wdeprecated-literal-operator from clang++-20 for 'operator"" _c' (i.e. with space) [Closed]

> Username: MichaelChirico  
> Created at: 2025-03-12 17:09:34 UTC  
> Updated at: 2025-03-14 00:00:39 UTC  
> Closed at: 2025-03-14 00:00:39 UTC  
> Url: https://github.com/boostorg/yap/issues/112  

https://github.com/boostorg/yap/blob/7f9902805819603e3e655dbec7d54d30fdb9718e/include/boost/yap/algorithm_fwd.hpp#L180  
https://github.com/boostorg/yap/blob/7f9902805819603e3e655dbec7d54d30fdb9718e/include/boost/yap/user_macros.hpp#L819  
  
The fix _can_ be trivial, but I think there's some back-compatibility considerations I lack the context to adjudicate, see sister issues e.g.  
  
https://github.com/boostorg/hana/pull/521

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-03-13 23:56:07 UTC  
> Url: https://github.com/boostorg/yap/issues/112#issuecomment-2722952975  

Man, that is a crazy-ass issue.  I'm not complaining at you, mind you.  I'm complaining at EWG/the committee.  I'll try to work this in during this release cycle.  Thanks for reporting it, here and on Boost.Parser!
