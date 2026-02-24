# #416 Enable building boost.hana with VS2017 15.8 [Closed]

> Username: xiangfan-ms  
> Created at: 2018-08-10 22:48:00 UTC  
> Updated at: 2018-09-01 00:27:20 UTC  
> Closed at: 2018-08-23 00:06:45 UTC  
> Url: https://github.com/boostorg/hana/pull/416  

### Here is the test result:  
  
    100% tests passed, 0 tests failed out of 1103  
  
### Here are the details of the five source workarounds:  
  
1. Active issues we are working on  
  
a. Multiple copy/move ctors  
VC doesn't correctly handle multiple copy/move ctors.  
The workaround is under macro BOOST_HANA_WORKAROUND_MSVC_MULTIPLECTOR_106654.  
  
b. Forward declaration of class template member function returning decltype(auto) (this issue is exposed by a recent change in boost 1.68)  
To deduce the actual return type, the compiler expects the function definition to be on the pending list for temploid, which isn't always the case when generic lambda is involved.  
The workaround is under macro BOOST_HANA_WORKAROUND_MSVC_DECLTYPEAUTO_RETURNTYPE_662735.  
  
2. Issues fixed in the development branch of MSVC  
  
Parsing template id  
VC sometimes incorrectly parses a comparison operation as a template id.  
The workaround is under macro BOOST_HANA_WORKAROUND_MSVC_RDPARSER_TEMPLATEID_616568.  
  
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

## Review 1 [Commented]

> Username: ldionne  
> Created_at: 2018-08-13 15:21:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/416#pullrequestreview-145704885  

📁 include/boost/hana/basic_tuple.hpp

```diff
 203 |             return drop_front_helper<N::value>(static_cast<Xs&&>(xs), std::make_index_sequence<
 204 |+ #ifdef BOOST_HANA_WORKAROUND_MSVC_RDPARSER_TEMPLATEID_616568
 205 |+                 (N::value < len) ? len - N::value : 0
```

> Username: ldionne  
> Created_at: 2018-08-13 15:21:52 UTC  
> Updated_at: 2018-08-16 19:47:50 UTC  
> Url: https://github.com/boostorg/hana/pull/416#discussion_r209651094  

Don't guard this one behind a macro -- just apply the workaround as-is. Once we set up an Appveyor job, it will make it impossible to regress.


---

## Review 2 [Commented]

> Username: ldionne  
> Created_at: 2018-08-13 15:22:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/hana/pull/416#pullrequestreview-145705378  

📁 include/boost/hana/config.hpp

```diff
  98 | #if (__cplusplus < 201400)
  99 | #   if defined(_MSC_VER)
 100 |+ #if _MSC_VER < 1915
```

> Username: ldionne  
> Created_at: 2018-08-13 15:22:59 UTC  
> Updated_at: 2018-08-16 19:47:50 UTC  
> Url: https://github.com/boostorg/hana/pull/416#discussion_r209651531  

Use `if defined(_MSC_VER) && _MSC_VER < 1915` instead

> Username: xiangfan-ms  
> Created_at: 2018-08-13 18:55:08 UTC  
> Updated_at: 2018-08-16 19:47:50 UTC  
> Url: https://github.com/boostorg/hana/pull/416#discussion_r209720603  

Then it will pick the #else part which is for GNU compiler :-)  
  
The first #if is to determine how to emit the warning (via #pragma message or #warning). The second #if is to determine whether to emit the warning.

> Username: MarcelRaad  
> Created_at: 2018-08-13 19:23:02 UTC  
> Updated_at: 2018-08-16 19:47:50 UTC  
> Url: https://github.com/boostorg/hana/pull/416#discussion_r209728448  

Or maybe just require `/Zc:__cplusplus` so that it gets set to the correct value? The message doesn't really apply to MSVC anyway.

> Username: xiangfan-ms  
> Created_at: 2018-08-13 19:30:14 UTC  
> Updated_at: 2018-08-16 19:47:50 UTC  
> Url: https://github.com/boostorg/hana/pull/416#discussion_r209730458  

I considered that option too. However, we discovered that various libraries don't work with this flag (either compiler bugs or source issues), so people may choose not to use this flag and I prefer to support boost.hana even if this flag is not used.  
  
I don't have strong preference, though.


---

## Comment 3

> Username: ldionne  
> Created_at: 2018-08-16 13:14:59 UTC  
> Url: https://github.com/boostorg/hana/pull/416#issuecomment-413541323  

Please rebase your branch on top of `boostorg:develop` and update the PR.

---

## Comment 4

> Username: xiangfan-ms  
> Created_at: 2018-08-16 19:54:39 UTC  
> Url: https://github.com/boostorg/hana/pull/416#issuecomment-413665338  

I rebased back_port branch using,  
  
git rebase --onto upstream/develop upstream/master

---

## Comment 5

> Username: ldionne  
> Created_at: 2018-08-22 23:42:33 UTC  
> Updated_at: 2018-08-22 23:42:48 UTC  
> Url: https://github.com/boostorg/hana/pull/416#issuecomment-415223211  

Locally, you need to run (where `boostorg` is the remote for boostorg/hana): (untested)  
  
```  
git checkout develop  
git pull boostorg develop  
git rebase develop back_port  
git push origin back_port --force  
```  
  
The bottom line is that I don't want commits from `master` to be included in this PR.

---

## Comment 6

> Username: xiangfan-ms  
> Created_at: 2018-08-23 00:05:20 UTC  
> Url: https://github.com/boostorg/hana/pull/416#issuecomment-415227592  

Sorry for the PR spam. I created a new PR based on the develop branch + cherry-pick (instead of rebase).

---
