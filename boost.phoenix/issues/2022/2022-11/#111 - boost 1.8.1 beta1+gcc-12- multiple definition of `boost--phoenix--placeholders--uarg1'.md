# #111 - boost 1.8.1 beta1+gcc-12: multiple definition of `boost::phoenix::placeholders::uarg1' [Open]

> Username: sgn  
> Created at: 2022-11-25 17:20:29 UTC  
> Updated at: 2023-12-14 07:56:31 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111  

Hi,  
  
I tried to recompile [freeorion](https://github.com/freeorion/freeorion) v0.4.10.2 with boost 1.81.0.beta1. The linking step run into this problem (stripped duplicated (in meaning) errors):  
  
> /usr/bin/ld: CMakeFiles/freeorionparseobj.dir/parse/VisibilityValueRefParser.cpp.o:(.bss+0x12): multiple definition of `boost::phoenix::placeholders::uarg1'; CMakeFiles/freeorionparseobj.dir/parse/ArithmeticRules.cpp.o:(.bss+0x12): first defined here  
  
It seems like the `boost::phoenix::placeholders::uarg1` (and friends) is allocated in bss in all translation unit that include `boost/phoenix.hpp`  
  
Is it intended?  
  
fwiw, the same code can be linked with gcc 10 and boost 1.80

---

## Comment 1

> Username: djowel  
> Created at: 2022-11-25 23:35:17 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1327930128  

There are no such changes AFAIK. Could you be more specific and point to the code or commit where the failure started using git bisect?

---

## Comment 2

> Username: Kojoley  
> Created at: 2022-11-26 17:43:30 UTC  
> Updated at: 2022-11-26 17:44:21 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1328086640  

> There are no such changes AFAIK. Could you be more specific and point to the code or commit where the failure started using git bisect?  
  
There was, it is https://github.com/boostorg/phoenix/pull/104 that added uarg* placeholders, I don't see them useful though.  
  
https://github.com/boostorg/phoenix/compare/boost-1.80.0...boost-1.81.0.beta1

---

## Comment 3

> Username: sgn  
> Created at: 2022-11-27 03:46:42 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1328164303  

Yes, revert #104 fixes the build

---

## Comment 4

> Username: djowel  
> Created at: 2022-11-27 11:39:29 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1328227723  

Please file a PR that reverts this. That way we can notify the author of the change as well.

---

## Comment 5

> Username: sgn  
> Created at: 2022-11-27 14:37:21 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1328260267  

See #112

---

## Comment 6

> Username: killerbot242  
> Created at: 2022-12-16 17:50:03 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1355306058  

I can confirm this issue is also present in the official release of 1.81 and as such breaks our codebase .  
What's the way to resolution ?

---

## Comment 7

> Username: sgn  
> Created at: 2022-12-16 17:54:23 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1355314035  

@killerbot242 Either applying the patch in #112 or below patch:  
```diff  
See https://github.com/boostorg/phoenix/issues/111  
Index: boost-1.81.0.beta1/boost/phoenix/stl.hpp  
===================================================================  
--- boost-1.81.0.beta1.orig/boost/phoenix/stl.hpp  
+++ boost-1.81.0.beta1/boost/phoenix/stl.hpp  
@@ -11,6 +11,5 @@  
   
 #include <boost/phoenix/stl/algorithm.hpp>  
 #include <boost/phoenix/stl/container.hpp>  
-#include <boost/phoenix/stl/tuple.hpp>  
   
 #endif  
```

---

## Comment 8

> Username: sgn  
> Created at: 2022-12-16 17:56:07 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1355315671  

Or you can `-DBOOST_PHOENIX_STL_TUPLE_H_` to avoid the inclusion of `boost/phoenix/stl/tuple.hpp`

---

## Comment 9

> Username: jwakely  
> Created at: 2023-03-14 16:29:45 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1468431643  

Would this work to fix this and #115   
  
```diff  
--- /usr/include/boost/phoenix/stl/tuple.hpp~   2023-03-14 16:25:22.341829104 +0000  
+++ /usr/include/boost/phoenix/stl/tuple.hpp    2023-03-14 16:27:12.933921685 +0000  
@@ -106,14 +106,16 @@  
         tuple_detail::idx_wrap<N>(), t);  
     }  
   
+#ifndef BOOST_PHOENIX_NO_PREDEFINED_TERMINALS  
     // Make unpacked argument placeholders  
     namespace placeholders {  
         #define BOOST_PP_LOCAL_LIMITS (1, BOOST_PHOENIX_ARG_LIMIT)  
         #define BOOST_PP_LOCAL_MACRO(N)                                                \  
-            auto uarg##N =                                                             \  
+            const auto uarg##N =                                                             \  
             boost::phoenix::get_<(N)-1>(boost::phoenix::placeholders::arg1);  
         #include BOOST_PP_LOCAL_ITERATE()  
     }  
+#endif  
 }} // namespace boost::phoenix  
   
 #endif // C++ 14  
```  
  
Making the placeholders const still isn't right, as it gives them internal linkage and so inline functions and templates using them are susceptible to ODR violations. But "harmless" ones, unless used with C++20 modules. Harmless ODR violations are better than harmful ODR violations that make the code completely unusable due to linker errors.

---

## Comment 10

> Username: tobias-loew  
> Created at: 2023-08-27 17:18:11 UTC  
> Updated at: 2023-08-27 17:19:06 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1694718712  

The ODR-violation is still present in 1.83  
  
> Would this work to fix this and #115  
>   
> ```diff  
> --- /usr/include/boost/phoenix/stl/tuple.hpp~   2023-03-14 16:25:22.341829104 +0000  
> +++ /usr/include/boost/phoenix/stl/tuple.hpp    2023-03-14 16:27:12.933921685 +0000  
> @@ -106,14 +106,16 @@  
>          tuple_detail::idx_wrap<N>(), t);  
>      }  
>    
> +#ifndef BOOST_PHOENIX_NO_PREDEFINED_TERMINALS  
>      // Make unpacked argument placeholders  
>      namespace placeholders {  
>          #define BOOST_PP_LOCAL_LIMITS (1, BOOST_PHOENIX_ARG_LIMIT)  
>          #define BOOST_PP_LOCAL_MACRO(N)                                                \  
> -            auto uarg##N =                                                             \  
> +            const auto uarg##N =                                                             \  
>              boost::phoenix::get_<(N)-1>(boost::phoenix::placeholders::arg1);  
>          #include BOOST_PP_LOCAL_ITERATE()  
>      }  
> +#endif  
>  }} // namespace boost::phoenix  
>    
>  #endif // C++ 14  
> ```  
>   
> Making the placeholders const still isn't right, as it gives them internal linkage and so inline functions and templates using them are susceptible to ODR violations. But "harmless" ones, unless used with C++20 modules. Harmless ODR violations are better than harmful ODR violations that make the code completely unusable due to linker errors.  
  
What about making them `inline` instead of `const` for C++17 (or higher) compilations? Wouldn't this solve all ODR problems?

---

## Comment 11

> Username: djowel  
> Created at: 2023-08-28 00:25:31 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1694814329  

Let's fix this once and for all, or just roll back to a stable state. Perhaps `constexpr`, although that would be pervasive and would require C++14. Just `const` would give the least impact.

---

## Comment 12

> Username: djowel  
> Created at: 2023-08-28 00:34:14 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1694818419  

Pushed the 'const' fix to develop.

---

## Comment 13

> Username: djowel  
> Created at: 2023-08-29 06:40:01 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1696857034  

> Pushed the 'const' fix to develop.  
  
Duh. Proto errors! I disabled the placeholders for now, due to lack of time. I'll get back to it later. I just don't want ODR violations lingering too long.

---

## Comment 14

> Username: LowLevelMahn  
> Created at: 2023-09-09 11:44:17 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1712491813  

i've added a (maybe) related comment to a commit: https://github.com/boostorg/phoenix/commit/665047aac26ad4d96b266d87504b3a88ad21b37e#commitcomment-126878034

---

## Comment 15

> Username: LowLevelMahn  
> Created at: 2023-11-05 06:24:09 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793650691  

still happens with current develop - 2023/11/05 - so it seems that also boost 1.84 will contain that bug  
do anyone got an solution to fix that - i can't upgrade from 1.80 because of this

---

## Comment 16

> Username: djowel  
> Created at: 2023-11-05 10:14:58 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793693840  

Darned! What are the errors? The `uarg`s are already #ifdef-ed out:   
  
https://github.com/boostorg/phoenix/commit/8913607a3788cb82d48ed461ea59c919b7bad3df

---

## Comment 17

> Username: Lastique  
> Created at: 2023-11-05 11:04:20 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793704903  

> Darned! What are the errors? The `uarg`s are already #ifdef-ed out:  
>   
> [8913607](https://github.com/boostorg/phoenix/commit/8913607a3788cb82d48ed461ea59c919b7bad3df)  
  
This commit is not in master. Please merge.

---

## Comment 18

> Username: djowel  
> Created at: 2023-11-05 11:06:43 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793705474  

> > Darned! What are the errors? The `uarg`s are already #ifdef-ed out:  
> > [8913607](https://github.com/boostorg/phoenix/commit/8913607a3788cb82d48ed461ea59c919b7bad3df)  
>   
> This commit is not in master. Please merge.  
  
Done.

---

## Comment 19

> Username: jwakely  
> Created at: 2023-11-05 11:27:07 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793709622  

> This commit is not in master. Please merge.  
  
This. Keeps. Happening.  
  
The typical boost git workflow relies on manually cherry picking or merging every fix to the master branch. And it constantly goes wrong so that releases keep being made from master with known bugs that were fixed months ago on the develop branch.  
  
This is a systemic problem.

---

## Comment 20

> Username: djowel  
> Created at: 2023-11-05 12:46:21 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793727531  

> > This commit is not in master. Please merge.  
>   
> This. Keeps. Happening.  
>   
> The typical boost git workflow relies on manually cherry picking or merging every fix to the master branch. And it constantly goes wrong so that releases keep being made from master with known bugs that were fixed months ago on the develop branch.  
>   
> This is a systemic problem.  
  
This is my fault. I'm so sorry. I haven't been paying careful attention to what's happening in Boost recently :-(

---

## Comment 21

> Username: jwakely  
> Created at: 2023-11-05 12:58:50 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793730306  

It's not just you, and it's an easy mistake to make because of the typical git workflow used by boost libs.  
  
The "all work happens in develop and all releases happen from master and every change must be manually merged" model is just error prone.

---

## Comment 22

> Username: djowel  
> Created at: 2023-11-05 13:27:28 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793737703  

> It's not just you, and it's an easy mistake to make because of the typical git workflow used by boost libs.  
>   
> The "all work happens in develop and all releases happen from master and every change must be manually merged" model is just error prone.  
  
I agree.

---

## Comment 23

> Username: Lastique  
> Created at: 2023-11-05 13:30:49 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793738480  

The develop is supposed to test the changes against other libraries before merging to master. This includes the library that is modified, as well as other libraries that use the library. This is different from testing a feature branch - the feature branch is not used by other libraries in their CI.  
  
I'm not sure how useful this develop testing is for other developers, but I did catch issues with other (i.e. not maintained by me) libraries that happened in develop before they were merged to master. So maybe this is not entirely worthless.  
  
But I agree that missing merges to master is a recurring issue. Maybe we should work on configuring GitHub Actions that will automatically merge develop to master once CI passes. Though that would make my point above a bit less relevant.

---

## Comment 24

> Username: LowLevelMahn  
> Created at: 2023-11-05 15:57:58 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793775720  

> still happens with current develop - 2023/11/05 - so it seems that also boost 1.84 will contain that bug do anyone got an solution to fix that - i can't upgrade from 1.80 because of this  
  
builds and links (and runs) without a problem with boost master - thank you

---

## Comment 25

> Username: jwakely  
> Created at: 2023-11-05 22:35:44 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793868000  

We should probably move this to the devel mailing list, but ...  
  
> The develop is supposed to test the changes against other libraries before merging to master.  
  
How many other libraries (apart from those maintained by boost devs like you) actually test against `develop`? Could they test against `master`? That's closer to what will actually get released anyway.  
  
  
> This includes the library that is modified, as well as other libraries that use the library. This is different from testing a feature branch - the feature branch is not used by other libraries in their CI.  
  
You'd still get that with a trunk-based model where all development happens on a trunk, and you periodically branch from that for a release. The difference is that when you branch for a release, everything currently on the trunk (which has been tested by anybody testing that trunk) goes into the release. You don't have the manual step of remembering to merge each fix.

---

## Comment 26

> Username: Lastique  
> Created at: 2023-11-05 23:35:32 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1793883060  

> We should probably move this to the devel mailing list, but ...  
  
I've started a discussion on the boost-dev ML (see the "Do we still want develop branch?" thread). Feel free to comment.  
  
> > The develop is supposed to test the changes against other libraries before merging to master.  
>   
> How many other libraries (apart from those maintained by boost devs like you) actually test against `develop`?  
> Could they test against `master`? That's closer to what will actually get released anyway.  
  
Every Boost library's develop, every PR for a Boost library, every feature branch in a Boost library is tested against develop. Maintainers are free to configure their CI as they want, but it wouldn't make sense to test against master given that PRs and feature branches must be merged to develop first.  
  
As for libraries outside Boost, I'm not sure what they test against. My guess is that most of them consume an official release of Boost (possibly, in the form of distro packages) rather than Boost from git. Boost releases are made from master.  
  
> > This includes the library that is modified, as well as other libraries that use the library. This is different from testing a feature branch - the feature branch is not used by other libraries in their CI.  
>   
> You'd still get that with a trunk-based model where all development happens on a trunk, and you periodically branch from that for a release. The difference is that when you branch for a release, everything currently on the trunk (which has been tested by anybody testing that trunk) goes into the release. You don't have the manual step of remembering to merge each fix.  
  
I think, Boost used the trunk model in the early days, and it didn't work so well. Basically, making a release was a problem because the trunk was always broken, and fixing it took a lot of time. This was back in the CVS/SVN days, when CI was nonexistant, so maybe it would work better today. However, I would still prefer master to remain the "stable" code base. The question is rather whether develop actually adds something tangible towards that stability in the current git + CI workflow.

---

## Comment 27

> Username: LowLevelMahn  
> Created at: 2023-12-14 07:56:30 UTC  
> Url: https://github.com/boostorg/phoenix/issues/111#issuecomment-1855351992  

fixed with 1.84.0
