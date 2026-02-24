# #419 Enable building boost.hana with VS2017 15.8 [Merged]

> Username: xiangfan-ms  
> Created at: 2018-08-23 00:03:24 UTC  
> Updated at: 2018-08-29 13:29:42 UTC  
> Merged at: 2018-08-29 13:29:42 UTC  
> Closed at: 2018-08-29 13:29:42 UTC  
> Url: https://github.com/boostorg/hana/pull/419  

### Here is the test result:  
  
    100% tests passed, 0 tests failed out of 1103  
  
### Here are the details of the five source workarounds:  
  
1. Active issues we are working on  
  
Multiple copy/move ctors  
VC doesn't correctly handle multiple copy/move ctors.  
The workaround is under macro BOOST_HANA_WORKAROUND_MSVC_MULTIPLECTOR_106654.  
  
2. Issues fixed in the development branch of MSVC  
  
a. Parsing template id  
VC sometimes incorrectly parses a comparison operation as a template id.  
The workaround is under macro BOOST_HANA_WORKAROUND_MSVC_RDPARSER_TEMPLATEID_616568.  
  
b. Forward declaration of class template member function returning decltype(auto) (this issue is exposed by a recent change in boost 1.68)  
To deduce the actual return type, the compiler expects the function definition to be on the pending list for temploid, which isn't always the case when generic lambda is involved.  
The workaround is under macro BOOST_HANA_WORKAROUND_MSVC_DECLTYPEAUTO_RETURNTYPE_662735.  
  
3. Issues fixed conditionally  
  
a. Empty base optimization  
VC doesn't always do EBO (empty base optimization). Changing this will break the ABI of MSVC and we provide a __declspec(empty_bases) to enable EBO.  
We have a blog post on this: https://blogs.msdn.microsoft.com/vcblog/2016/03/30/optimizing-the-layout-of-empty-base-classes-in-vs2015-update-2-3/.  
Some tests in hana have static_assert on the size of certain types which relies on EBO being applied:  
  
hana\test\detail\ebo.cpp  
hana\test\issues\github_202.cpp  
hana\test\pair\empty_storage.cpp  
hana\test\tuple\empty_member.cpp  
  
The workaround is under macro BOOST_HANA_WORKAROUND_MSVC_EMPTYBASE.  
  
b. Variadic macro expansion  
The implementation of variadic macro isn't conformant and the macro expansion often results in incorrect result.  
The issue is fixed under /experimental:preprocessor and isn't on by default yet.  
We have a blog post on this: https://blogs.msdn.microsoft.com/vcblog/2018/07/06/msvc-preprocessor-progress-towards-conformance/.  
  
The workaround is under macro BOOST_HANA_WORKAROUND_MSVC_PREPROCESSOR_616033.  
  
### Here is the list of files impacted by the source workarounds:  
  
- BOOST_HANA_WORKAROUND_MSVC_MULTIPLECTOR_106654  
   hana\test\_include\laws\base.hpp  
   hana\test\map\cnstr.trap.cpp  
   hana\test\set\cnstr.trap.cpp  
   hana\test\tuple\cnstr.trap.cpp  
  
-  BOOST_HANA_WORKAROUND_MSVC_DECLTYPEAUTO_RETURNTYPE_662735  
   hana\test\_include\laws\euclidean_ring.hpp  
   hana\test\_include\laws\group.hpp  
   hana\test\_include\laws\monad_plus.hpp  
   hana\test\_include\laws\monoid.hpp  
   hana\test\_include\laws\ring.hpp  
  
-  BOOST_HANA_WORKAROUND_MSVC_RDPARSER_TEMPLATEID_616568  
   (this workaround is applied unconditionally based on review feedback)  
   hana\include\boost\hana\basic_tuple.hpp  
   hana\include\boost\hana\string.hpp  
   hana\include\boost\hana\tuple.hpp  
  
-  BOOST_HANA_WORKAROUND_MSVC_EMPTYBASE  
   hana\include\boost\hana\basic_tuple.hpp  
   hana\include\boost\hana\pair.hpp  
   hana\include\boost\hana\tuple.hpp  
   hana\include\boost\hana\detail\integral_constant.hpp  
   hana\test\detail\ebo.cpp  
  
-  BOOST_HANA_WORKAROUND_MSVC_PREPROCESSOR_616033  
   hana\include\boost\hana\detail\preprocessor.hpp  
   hana\include\boost\hana\detail\struct_macros.hpp  
  
  
### BTW,  
  
1. There are some warnings which I don't fix. I will likely address them in a separate PR. They look legit and don't impact the build and tests.  
  
2. Appveyor currently doesn't provide 15.8 Preview 5 which contains all the compiler fixes we made in the previous months. I plan to update appveyor.yml after Appveyor provides 15.8 RTM.

---

## Comment 1

> Username: xiangfan-ms  
> Created_at: 2018-08-23 00:04:31 UTC  
> Url: https://github.com/boostorg/hana/pull/419#issuecomment-415227443  

Sorry for the PR spam.  
I'm not very familiar with git, and I think it is safer to create a new PR based on develop branch.  
  
git branch develop origin/develop  
git checkout develop  
git pull https://github.com/boostorg/hana develop  
git cherry-pick 4a0f3bd4a901a66cfd28e09592556a2023c0d176  
git cherry-pick c4738fca1a0bb7fa36f5b5e06deaddb4db532468  
git cherry-pick d104bd631a5ed0a01d9f5e71a414da56b021d900  
git push  
  
Let me know if there are any issues.

---
