# #42 ADAPT_STRUCT macros supporting type deduction fixes for Clang [Merged]

> Username: daminetreg  
> Created at: 2014-11-18 23:23:42 UTC  
> Updated at: 2014-11-19 00:33:30 UTC  
> Merged at: 2014-11-19 00:33:30 UTC  
> Closed at: 2014-11-19 00:33:30 UTC  
> Url: https://github.com/boostorg/fusion/pull/42  

Hi @djowel  and @K-ballo,  
  
I've got the time as told in https://github.com/boostorg/fusion/pull/28#issuecomment-62301894, to test all again under clang and gcc.  
  
So with these changes (I made a terrible mistake, which is now fixed, but that g++ accepted, use of non-static variable in a delctype-unevaluated-context of a typedef), all tests pass now under :   
- clang (version : Ubuntu clang version 3.4-1ubuntu3~precise1 (tags/RELEASE_34/final) (based on LLVM 3.4))  
- g++ (Ubuntu 4.8.1-2ubuntu1~12.04) 4.8.1  
  
With the following bjam calls :   
1. `bjam -a`  
2. `bjam -a cxxflags="-DBOOST_PP_VARIADICS"`  
3. `bjam -a cxxflags="--std=c++11`  
4. `bjam -a cxxflags="--std=c++11 -DBOOST_PP_VARIADICS"`  
## Hidden change in this PR (commit https://github.com/daminetreg/fusion/commit/b5018586aad1475652702a41d4f317f4494bdda9 )  
  
I know it's bad to make non-focused changes in a PR but I can remove it if you think it's not possible to merge it with this PR.  
I made a change in the API of the non-documented BOOST_FUSION_ADAPT_ADT_NAMED and BOOST_FUSION_ADAPT_ASSOC_ADT_NAMED to be able to specify the get/set expression as : `obj.get` and not `obj.obj.get` as it was since 2010 in a commit of Hartmut Kaiser, I believe it's an error because the PROXY_TYPE_PREFIX was not put everywhere, and instead of fixing the implementation with Proxy types Hartmut changed the tests. What bothers me is why did he so ? Is it wanted to have to specify the proxy type prefix on call site ?  
## TODO  
  
I still have to check that compiling MSVC is okay, and I think I'll get the time for this next tuesday, so I may update this PR or make a new PR next week or in two week with the compilation for MSVC working.   
  
Have you any preference for versions of MSVC ? If you have no preference I'll do the job with an old version shipped with Visual Studio 2010 ultimate and with the last version supporting most of C++11.  
  
@djowel is the documentation I linked to correct or do you want me to improve it ?  
  
Thanks for the time you take for reviewing my changes :smile:

---

## Comment 1

> Username: djowel  
> Created_at: 2014-11-19 00:33:21 UTC  
> Url: https://github.com/boostorg/fusion/pull/42#issuecomment-63572773  

Great! No preference for MSVC version here.

---
