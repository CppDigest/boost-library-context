# #28 - Dependencies not being found with new modular boost [Closed]

> Username: jzmaddock  
> Created at: 2025-04-15 17:55:48 UTC  
> Updated at: 2025-04-21 08:04:13 UTC  
> Closed at: 2025-04-21 08:04:12 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28  

I'm not sure who or what is to blame, but the modular build system changes seem to be injecting all sorts of previously unseen dependencies which this tool is not finding.  
  
For example the type_traits CI tests are currently all broken due to a missing dependency to boost.detail: https://github.com/boostorg/type_traits/actions/runs/14475908609/job/40601374907?pr=197  
  
I have no idea where this has come from, or whether it is actually needed, but boostdep is clearly missing it.  
  
@grafikrobot

---

## Comment 1

> Username: grafikrobot  
> Created at: 2025-04-15 18:16:42 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2807086648  

Hm.. This could be an outdated dependency. Since the modular PRs have been live for a really long time what the dependency report said at the time vs. the reality at the present could differ. I'm investigating.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-04-15 18:52:44 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2807176270  

I don't think there's a dependency between TypeTraits and Detail, so boostdep shouldn't be finding one.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2025-04-15 19:06:47 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2807210516  

> I don't think there's a dependency between TypeTraits and Detail, so boostdep shouldn't be finding one.  
  
Indeed. And I'm failing to find where that build reference is coming from. Since my original PR was working. So, it's weird.

---

## Comment 4

> Username: pdimov  
> Created at: 2025-04-15 19:14:28 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2807232202  

This is where it's coming from:  
  
https://github.com/boostorg/config/blob/cc87b132f8062939bcd2a2ade7d30dcb149648f3/test/Jamfile.v2#L19  
  
The TypeTraits CI builds "config_info_travis_install" from libs/config/test, and you're making it depend on Detail because the dependency in on the project.

---

## Comment 5

> Username: grafikrobot  
> Created at: 2025-04-15 19:22:09 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2807250975  

Yeah, that's it. No way depinst could see that dependency.

---

## Comment 6

> Username: grafikrobot  
> Created at: 2025-04-15 19:27:33 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2807262810  

@jzmaddock i.e. you'll need to tell to depinst about the out-of-band dependencies to install.

---

## Comment 7

> Username: pdimov  
> Created at: 2025-04-15 19:30:50 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2807270488  

It couldn't, but it also shouldn't, because there's no dependency between "config_info" and Detail.  
```  
C:\boost-git\develop>dist\bin\boostdep --track-tests config  
Primary dependencies for config:  
  
core:  
    <boost/core/lightweight_test.hpp>  
        from <libs/config/test/helper_macros_test.cpp>  
        from <libs/config/test/limits_test.cpp>  
    <boost/detail/lightweight_test.hpp>  
        from <libs/config/test/cstdint_test.cpp>  
        from <libs/config/test/cstdint_test2.cpp>  
  
detail:  
    <boost/detail/lightweight_main.hpp>  
        from <libs/config/test/limits_test.cpp>  
  
type_traits:  
    <boost/type_traits/alignment_of.hpp>  
        from <libs/config/test/math_info.cpp>  
```  
Only "limits_test" depends on Detail, and only "math_info.cpp" depends on TypeTraits.  
  
These dependencies should be removed from the project and only added to the appropriate targets.

---

## Comment 8

> Username: grafikrobot  
> Created at: 2025-04-15 19:50:48 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2807324057  

https://github.com/boostorg/config/pull/515

---

## Comment 9

> Username: grafikrobot  
> Created at: 2025-04-20 20:56:04 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2817336520  

@jzmaddock is this resolved?

---

## Comment 10

> Username: jzmaddock  
> Created at: 2025-04-21 08:04:12 UTC  
> Url: https://github.com/boostorg/boostdep/issues/28#issuecomment-2817890842  

Yes I believe so, thanks!
