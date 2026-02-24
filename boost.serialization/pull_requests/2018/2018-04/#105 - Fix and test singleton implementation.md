# #105 Fix and test singleton implementation [Closed]

> Username: Flamefire  
> Created at: 2018-04-29 12:48:21 UTC  
> Updated at: 2024-12-09 10:29:16 UTC  
> Closed at: 2018-11-08 06:54:16 UTC  
> Url: https://github.com/boostorg/serialization/pull/105  

As described in #104 the singleton implementation was broken in https://github.com/boostorg/serialization/commit/b0a794da38b52c8ae0f8155cf3bb30321cf67f00 which led to crashes especially in the context of (multiple) shared libraries.  
  
The "fix" allocating the instance on the heap basically just never freed the instances and hence leaked memory. This was due to not understanding the root cause of the problem.  
  
To summarize:     
There are 2 use cases for the singleton: On where the type `T` is derived from `singleton<T>` and one where `singleton<T>` is used without deriving `T` from it. The above commit put the `is_destroyed` handling into the ctor/dtor of `singleton<T>`. However if `T` is not derived from it, then `singleton<T>` is never instantiated and hence `is_destroyed` is never set. See also some discussion in #104 and #79.  
  
The crash/memory corruption now happens (or happened before the leaky implementation) if the `tkmap` used from `extended_type_info_typeid` is destroyed first. As it used from the ctor of `extended_type_info_typeid` this should normally not happen. However it does happen when used from multiple shared libraries under (at least) GCC 5.4 and 7.1. The reason is currently unknown (see https://stackoverflow.com/questions/50064617/order-of-destruction-for-static-function-members-in-shared-libraries) but we can just assume, the order of destruction is undefined. Hence the need for the `is_destroyed` flag.  
  
On this PR:  
  
- It is built on top of #94 to enable more testing  
- It also tests in release mode to ensure that e.g. stripping of symbols by the linker does not cause problems  
- Add a test case to ensure, that singletons are properly constructed, destroyed and the `is_destroyed` flag is correctly set  
- Add a test case to test the same for shared libraries  
- Add a test case based on https://gist.github.com/Flamefire/286e9e0e501731a04f10786450d3e711 to test the issue with multiple shared libraries. Before this PR this caused a double-free which is detected by the debug-glibc. It would be even better to run this in valgrind, but I'm not familiar enough with the Jam-Files and windows doesn't have it.  
- Fix the failing testcases by partially reverting https://github.com/boostorg/serialization/commit/b0a794da38b52c8ae0f8155cf3bb30321cf67f00 :  
- Reintroduce `detail:singleton_wrapper` with the correct `is_destroyed` setting in the dtor. It uses a method-local variable to enforce initialization on first use (preferred way to avoid static init disaster https://isocpp.org/wiki/faq/ctors#construct-on-first-use-v2)   
- Replace heap-allocated variable by static instance (see isocpp FAQ, avoids static init disaster and is thread safe)  
- Assert `!is_destroyed` on enter of `get_instance`. Note that the comment was wrong. It did not refer to the instance and is even removed in non-debug builds  
- Make the ctor of `singleton` protected and default to avoid accidental misuse (it might never be called if `T` is not derived from `singleton<T>`)  
- Excessively comment everything to avoid breaking changes by misunderstandings  
  
Other notes:  
  
- The comment on the heap allocated variable was wrong: as `singleton_wrapper` is the most derived type of the instance it will always be destructed before `T` and `singleton<T>` (if `T` is derived from it) There is nothing one can do about it!  
- Valgrind tests would be nice  
- static instance is superior to static pointer (see isocpp FAQ), it is not about visibility, double-destruction or anything else. It simply makes sure that the instance is actually destructed at program exit  
- Again: It is safe to assume the destruction order is undefined. Hence all asserts assuming otherwise have been removed (they had a runtime check afterwards anyway which is the correct way)  
- Not deriving from `singleton<T>` but using it directly is potentially dangerous as you can have multiple `T` instances and you have only 1 `singleton<T>` where you might not expect it. Example: `extended_type_info` uses a `singleton<detail::tkmap>` to work around this issue because `extended_type_info_typeid` already uses a `singleton<tkmap>`. It might be wise to enforce deriving from `singleton<T>` and using `T::get_instance()` or at least do that in the library.  
  
Finally: Please try to break this! I'm pretty sure I covered everything by using TDD but I might have missed something.

---

## Comment 1

> Username: Flamefire  
> Created_at: 2018-05-01 14:50:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-385689775  

@robertramey Note how this also fixes the current test failure in `test_dll_export`. The failure is a segmentation fault as described here and in #104

---

## Comment 2

> Username: tobias-loew  
> Created_at: 2018-05-11 00:42:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-388226260  

I'm absolutely in favor for this PR. It essentially removes the changes from https://github.com/boostorg/serialization/pull/79. Since the destruction sequence is well defined in the standard [basic.start.term] (and nobody else before had this problem), it seems to be a non-standard-conformance of gcc.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2018-05-11 10:39:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-388328213  

@tobias-loew The description in #79 is simply wrong. The `singleton_wrapper` will always be cleaned up first as it is the most derived type. The inheritance is: `singleton_wrapper->Foo(->singleton<Foo>)` (last is optional, see my comments in the src here)  
  
On standard destruction order: Normally it is all good. Just in case of multiple shared libraries the problem occurs. See https://stackoverflow.com/questions/50064617/order-of-destruction-for-static-function-members-in-shared-libraries There I show how the same types are destructed together although the defined destruction order should not allow this. Although this should be blamed on GCC (or whatever) one cannot ignore it. Hence the need for `is_destroyed` and tests for it.

---

## Comment 4

> Username: tobias-loew  
> Created_at: 2018-05-11 21:29:01 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-388490522  

@Flamefire I agree that #79  is screwed up. But I'd really like to see a gcc-workaround-macro here and a comment explaining what's going on.  
The standard explicitly specifies that the dtor are sequenced in opposite order to the *completion* of the ctors:  
  
"If the completion of the constructor or dynamic initialization of an object with static storage duration  
strongly happens before that of another, the completion of the destructor of the second is sequenced before the initiation of the destructor of the first."  
  
And the stackoverflow-example shows that gcc seems to have trouble obeying that.  
This is a bug in gcc and not in serialization, so let's not put a burden on standard conforming compilers.

---

## Comment 5

> Username: Flamefire  
> Created_at: 2018-05-12 18:20:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-388573495  

The `is_destroyed` is actually the work-around and it works well. However it is also a good general method to avoid problems at all: If you have a class, that is used as a singleton and that accesses other singletons in its dtor, first check the `is_destroyed` of the accessed singleton and if set, don't use it.  
  
I agree that it is a bug in GCC, but we cannot ignore it. I won't call the `is_destroyed` method a burden and IMO it is well-suited to avoid problems (maybe other compilers or specific versions suffer from the same problem? How to be sure it is only a GCC problem?)  
  
From my tests GCC 6.4 and 7.1 are affected. Triggering the bug with a simple test case seems to be quite hard. It requires static boost, but dynamic libraries. But besides that I'm not quite sure.

---

## Comment 6

> Username: Flamefire  
> Created_at: 2018-05-15 12:45:21 UTC  
> Updated_at: 2018-05-15 14:41:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-389153279  

I found that Clang is also affected. It seems to be a general problem or dependent on e.g. GLIBC (using 2.12 and 2.23 here)  
  
For the `test_multi_shared_libs` I'd actually want to reproduce what I reported [on StackOverflow](https://stackoverflow.com/questions/50064617/order-of-destruction-for-static-function-members-in-shared-libraries) For that I need to link the shared libraries against the currently build boost or at least against static Boost.Serialization. One can add `/<link>static` to https://github.com/boostorg/serialization/pull/105/files#diff-e97299ad55a4013dc19889043aa464a5R58 (and same for the 2nd library) But this requires testing with `cxxflags=-fPIC`. Not sure if this is acceptable.     
With that change and without the patch the testcase crashes everytime reproducing the behavior I mentioned and maybe at least similar to the current segmentation fault of `test_dll_exported` although I wasn't able to confirm the behavior for shared Boost.Serialization so `test_dll_exported` might be something else. However as this PR also fixes `test_dll_exported` I'm quite sure it is pretty much the same.  
  
@robertramey Could you tell me what you need to get some progress here? Or is there another maintainer who is able to verify this PR?

---

## Comment 7

> Username: MikeGitb  
> Created_at: 2018-06-25 09:03:40 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-399882864  

Just to make sure I understand this correctly: The leaky version only leaks when a shared library is unloaded - correct? Not saying this is ideal, but is this really something you have to worry about?

---

## Comment 8

> Username: Flamefire  
> Created_at: 2018-06-25 09:27:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-399889535  

This is correct. The heap-allocated instance is never freed and hence leaks on terminate/unload. It is a problem once you have a long running app that loads/unloads the library multiple times. There are people doing that and for serialization related stuff it makes sense. Some user complaint here: https://github.com/boostorg/serialization/issues/104#issuecomment-387835322

---

## Comment 9

> Username: davydden  
> Created_at: 2018-08-20 15:45:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414364665  

This PR also fixes the segmentation fault with 1.68.0 (which was not the case with 1.67.0) we have in a simple test in https://github.com/dealii/dealii/issues/7074 . @masterleinad can probably point to the configure test which I can't find at the moment.

---

## Comment 10

> Username: masterleinad  
> Created_at: 2018-08-20 16:05:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414371423  

> @masterleinad can probably point to the configure test which I can't find at the moment.  
  
It's [here](https://github.com/dealii/dealii/tree/master/cmake/configure/TestBoostBug).

---

## Comment 11

> Username: Flamefire  
> Created_at: 2018-08-20 16:57:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414388106  

Thanks for the additional input and the confirmation, that this PR works for you too.    
Note: See #111 which is a test for the crash. Also note that 1.65 introduced the crash, changed to a memory leak in 1.66 to 1.67

---

## Comment 12

> Username: masterleinad  
> Created_at: 2018-08-20 17:00:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414389098  

Yes, 1.67 worked. I wonder what changed since then such that it is crashing again.

---

## Comment 13

> Username: Flamefire  
> Created_at: 2018-08-20 17:03:41 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414390103  

The commit from 1.66 was reverted, changing the memory leak back to a crash: https://github.com/boostorg/serialization/commit/8ca532a4d5b43f9e468a1a2a36dfb489e9ecc422

---

## Comment 14

> Username: davydden  
> Created_at: 2018-08-20 18:40:39 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414420228  

it looks like this PR works for everyone, perhaps @robertramey  can review and merge it?

---

## Comment 15

> Username: tobias-loew  
> Created_at: 2018-08-21 08:31:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414595280  

Sorry to crash the party, but I'm really surprised how this PR can fix the crash with gcc-builds.  
Essentially, this PR changes the singletons ctor from public to protected there is no other semantic change in singleton.hpp. (Moving the singleton_wrapper and is_destoyed-flag to another class doesn't change the semantics at all as "detail" namespaces don't belong to the public interface.)  
There is just one tiny syntatic change for release builds: is_destroyed won't get initialized eagerly during construction of the singleton but only when explicitly accessed or during destruction of the singleton.  
And finally a comment to the reverted changes from 1.66: most programmers are concerned about memory leaks (e.g. with tons of "unimportant" memory leaks from singletons in the output you may miss the important one, that lets your program crash after a month)

---

## Comment 16

> Username: davydden  
> Created_at: 2018-08-21 08:34:56 UTC  
> Updated_at: 2018-08-21 08:35:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414596053  

> but I'm really surprised how this PR can fix the crash with gcc-builds.  
  
it's [also](https://github.com/dealii/dealii/issues/7074#issue-351584069) `Intel 18.03` (paired with `gcc 4.8.5`) on Linux HPC.

---

## Comment 17

> Username: Flamefire  
> Created_at: 2018-08-21 18:11:54 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414770141  

> Moving the singleton_wrapper and is_destoyed-flag to another class doesn't change the semantics at all as "detail" namespaces don't belong to the public interface  
  
Wrong: See #110 which shows how `is_destroyed` is broken  
  
>  is_destroyed won't get initialized eagerly during construction of the singleton but only when explicitly accessed or during destruction of the singleton.  
  
You forgot: On first access (e.g. https://github.com/boostorg/serialization/pull/105/files#diff-c23e78320ecd29888bb77891a5943f41L54)  
  
> most programmers are concerned about memory leaks (e.g. with tons of "unimportant" memory leaks from singletons in the output you may miss the important one, that lets your program crash after a month)  
  
I'm more concerned about a crash than a memory leak.

---

## Comment 18

> Username: Flamefire  
> Created_at: 2018-08-21 19:05:10 UTC  
> Updated_at: 2018-08-22 06:24:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414787135  

Rebased on current develop, #110 and #111, added the remaining test and applied the fixes last.  
  
Please note the failure of aac07cf and how this is fixed

---

## Comment 19

> Username: tobias-loew  
> Created_at: 2018-08-22 09:33:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414972843  

>> Moving the singleton_wrapper and is_destoyed-flag to another class doesn't change the semantics at all as "detail" namespaces don't belong to the public interface  
  
>Wrong: See #110 which shows how is_destroyed is broken  
  
I checked #110 with Boost 1.63 and 1.67 with VS 2015 and it runs fine with BOTH versions.   
  
>>is_destroyed won't get initialized eagerly during construction of the singleton but only when explicitly accessed or during destruction of the singleton.  
  
>You forgot: On first access (e.g. https://github.com/boostorg/serialization/pull/105/files#diff-c23e78320ecd29888bb77891a5943f41L54)  
  
this seems to be a case of "explicitly accessed"  
  
>>most programmers are concerned about memory leaks (e.g. with tons of "unimportant" memory leaks from singletons in the output you may miss the important one, that lets your program crash after a month)  
  
>I'm more concerned about a crash than a memory leak.  
  
This may be true for the **single** case where it's either memory-leak or crash. But here we have crashes of some against memory-leaks of the whole Boost-community, and that's a different story. You shouldn't make your code run on the expense of others.  
This is still a non-conforming-compiler problem and should be solved there. Boost allows workarounds for broken compilers, but they should be guarded by respective macros.

---

## Comment 20

> Username: Flamefire  
> Created_at: 2018-08-22 09:49:47 UTC  
> Updated_at: 2018-08-22 09:50:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-414977306  

> I checked #110 with Boost 1.63 and 1.67 with VS 2015 and it runs fine with BOTH versions.  
  
1.63 is expected to work, 1.65+ is not. If 1.67 worked for you, something is wrong on your side: See appveyor. Did you actually run all the tests? Reconfigured? Started from a clean checkout?  
  
> this seems to be a case of "explicitly accessed"  
  
Yes, missed this in your text. So what was your complaint about this?  
  
> But here we have crashes of some against memory-leaks of the whole Boost-community, and that's a different story. You shouldn't make your code run on the expense of others.  
  
Dito. You have a memory leak on shutdown or lib unload, we have crashes. So at whose expense should it be?     
BTW: The bugfix is here, the reasons are explained.  
  
> This is still a non-conforming-compiler problem and should be solved there. Boost allows workarounds for broken compilers, but they should be guarded by respective macros.  
  
Nope, its the runtime for ALL (AFAIK) linux runtimes. And the crash is a use-after-free/destroy which can easily be caught by fixing `is_destroyed` which I did here.

---

## Comment 21

> Username: MikeGitb  
> Created_at: 2018-08-22 12:10:43 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-415010390  

I'm starting to wonder where all this opposition against this fix is coming from. Does it generate problems for anyone? Is the code difficult to understand (and would thus be hard to maintain?).  
  
My understanding is that with this fix, there is neither a leak, nor a crash. Isn't that strictly better than having a leak or a crash (which makes the discussion about which is better rather pointless)

---

## Comment 22

> Username: tobias-loew  
> Created_at: 2018-08-23 09:29:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-415352856  

Sorry, if I made the impression to be against a solution for the crash/memory-leak. A version that works for all is absolutely preferable. (As MSVC-user with its many flaws I profited a lot from that in the boost-libs.)  
But I'm still a bit puzzled: the code for singleton.hpp in this PR is (apart from comments) very close to 1.63. the only changes I found:  
  
- the implementation of singleton_module's methods is made inline  
- m_is_destroyed was a static member of singleton_wrapper<T> and is now a static variable in singleton_wrapper<T>::get_is_destroyed()  
  
It is really good that such small changes solve the crash with gcc/clang on linux. Please, someone of the gcc/clang users notify the respective developers about this.  
  
@Flamefire   
>>I checked #110 with Boost 1.63 and 1.67 with VS 2015 and it runs fine with BOTH versions.  
  
>1.63 is expected to work, 1.65+ is not. If 1.67 worked for you, something is wrong on your side: See appveyor. Did you actually run all the tests? Reconfigured? Started from a clean checkout?  
  
Sorry and thanks for the hint, my 1.67 version was not clean. After resetting it "didn't work" as expected.

---

## Comment 23

> Username: Flamefire  
> Created_at: 2018-08-23 09:42:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-415356474  

> m_is_destroyed was a static member of singleton_wrapper and is now a static variable in singleton_wrapper::get_is_destroyed()  
  
This change is so that the same method as for singletons itself is used: A static function variable is preferred over a static member variable due to static initialization order fiasco. The singleton instance itself is also a static function variable for the same reason. But this is only part of the fix.  
  
There were 2 problems:  
  
1. `is_destroyed` returned wrong values as proven by #110 . This was due to changes introduced in 1.65(?) This is fixed by reverting most of that to the earlier, working version and verifying this extensively (see the amount of tests added)  
2. Handle static destruction order fiasco. Linux(?) has an issue, that destruction order in "some cases" (shared libs...) is not the same as for static binaries. So what happens is, that destructor of singleton A accesses singleton B, but B is already destroyed. We cannot fix the underlying problem (why B is destroyed before A), but we now have a working `is_destroyed` which can be used as a check before accessing B. This boils down to the equivalent of `if(objectPtr != nullptr) objectPtr->callFunction()` (see 5d3d714)

---

## Comment 24

> Username: roycekemp  
> Created_at: 2018-10-09 16:36:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-428262683  

Newbie Question: I have 1.68.0 installed (built from source tarball, not a git clone) and I am encountering this issue. How would I apply this fix? Are there patch files I can download?

---

## Comment 25

> Username: davydden  
> Created_at: 2018-10-09 17:15:22 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-428275537  

> Are there patch files I can download?  
  
we have one in Spack https://github.com/spack/spack/blob/develop/var/spack/repos/builtin/packages/dealii/boost_1.68.0.patch

---

## Comment 26

> Username: Flamefire  
> Created_at: 2018-10-09 18:28:52 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-428300207  

Note: You can simply add ".patch" to the PR URL: https://github.com/boostorg/serialization/pull/105.patch

---

## Comment 27

> Username: davydden  
> Created_at: 2018-10-09 19:02:26 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-428311688  

> You can simply add ".patch" to the PR URL:  
  
that won't apply to the boost tarbal, so some manual labor needed to transfer this PR to 1.68.0 release.

---

## Comment 28

> Username: roycekemp  
> Created_at: 2018-10-09 21:33:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-428360487  

> > Are there patch files I can download?  
>   
> we have one in Spack https://github.com/spack/spack/blob/develop/var/spack/repos/builtin/packages/dealii/boost_1.68.0.patch  
  
Brilliant. Worked like a charm! Big thanks to the community here, Flamefire especially for the root cause analysis.

---

## Comment 29

> Username: Rombur  
> Created_at: 2018-10-10 12:34:51 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-428554217  

So what will it take to get this patch merged? We basically now have a bunch of people who are patching their version of boost using this PR so I would say it's pretty well tested...

---

## Comment 30

> Username: robertramey  
> Created_at: 2018-10-12 19:00:42 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429427639  

Based on the patch referred to above, I'm made some changes.  I've tested them on Clang 7.1 and gcc 7.1 (its a coincidence that the version # are the same).  Looking good on Appveyor (windows), Travis and so far on the boost test matrix.  Still one failure - clang 9.1 on OSX?  I'm thinking this is a different error.  Happy to get feedback from everyone.  I'm not thanking anyone yet because I don't want anyone to give up their own testing.  We'll do this later when I feel I can close this.

---

## Comment 31

> Username: davydden  
> Created_at: 2018-10-12 19:16:08 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429432514  

> Based on the patch referred to above, I'm made some changes.  
  
Out of curiosity, why not test this PR as-is in its current form?

---

## Comment 32

> Username: robertramey  
> Created_at: 2018-10-12 19:35:02 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429438447  

The suggests a compiler issue related to the sequence of destructors of nested classes in the presence of DLLS.  The original patch seemed to work around the issue without really knowing what the problem was.  So I was concerned that the problem might well arise again - perhaps in a different guise.  In my version, I eliminated the class nesting entirely.  It wasn't really necessary as it turns out.  So I believe that this is  a more robust fix.  Remember this isn't the first time we've tried to fix this.  Finally, the current code is a lot simpler and easier to understand - something I always try to do while fixing something.

---

## Comment 33

> Username: robertramey  
> Created_at: 2018-10-12 19:36:20 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429438877  

BTW - I never doubted that the PR made the symptoms go away.  I just wanted to remove code rather than adding it.

---

## Comment 34

> Username: Flamefire  
> Created_at: 2018-10-13 07:02:08 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429517306  

>> Based on the patch referred to above, I'm made some changes.  
  
> Out of curiosity, why not test this PR as-is in its current form?  
  
I have the same question. You answer with   
  
>  The original patch seemed to work around the issue without really knowing what the problem was.   
  
You still do this!  
  
- I analysed the problem in full detail and found the reason/effect causing it.  
- I provided test cases showing the problems(!): #110/#111  
- I provided a reasonable fix for exactly the 2 issues discovered: https://github.com/boostorg/serialization/pull/105/commits/e1b68942b50c133432b29068fcd90e96c5f11963  
  
You keep ignoring this and instead of reviewing the actual (and by now tested) changes you keep adding own stuff without actually fixing the root problems and circumvent review procedures. Why is it allowed for the maintainer to simply push changes without any PR/review at all? Why roll an own solution when one exists instead of discussing the solution at hand? Do you really assume the code was made artificially complicated? Then comment on those lines and I can explain what and why it has be done!  
  
What is even worse: You mixed the changes to the singleton in an unrelated commit: https://github.com/boostorg/serialization/commit/e1893dd28c408bb25352596e2f91892b3d570164  
  
And `is_destroyed` is still broken. Hence my  
  
> You still do this! [working around the issue without really knowing what the problem is]  
  
So I ask again:     
Please review the test cases in #110 and #111 and merge or ask if anything is unclear.     
Please review the changes at hand and ask if anything is not to your liking. I may have valid reasons for that.

---

## Comment 35

> Username: robertramey  
> Created_at: 2018-10-13 17:34:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429560448  

Honestly, I never understood your argument that is_destroyed is broken.  I put a in a test which now runs all the time which verifies that all instances of singleton<T> are destroyed, and that they are destroyed in the inverse order of creation.  It's a pretty simple test and quite convincing to me.  
  
me:The original patch seemed to work around the issue without really knowing what the problem was.  
you:You still do this!  
  
I won't dispute this. I've come to believe that the problem is due to some combination unexplained compiler behavior associated with visibility, nested classes, interaction with the operating system, "automatic" dynamic loading and possibly ODR violations when some functions are inline and some are not and these are called from within the DLL and from the application.  In short, I don't think that the "root cause" is convincingly discernible.  
  
I've had unhappy experiences of merging PRs without really understanding them.  The most recent case is the original "fix" which fixed the test, but created memory leaks.  So I'm naturally a little skeptical.  Your explanation and associated PR is really too elaborate for me to understand.  You (and others) have provide enough information to make the problem repeatable and that has been essential.  I extracted what I believe to be the essence of your PR and it seems to resolve all test cases which were failing before.  The only failures now are some older (< 4.0) versions of clang.  I believe that these have always been failing, though our test database doesn't hold data to actually verify this.  So I believe that the original problem motivating this PR has been addressed while simplifying the code and making it easier to understand and maintain.

---

## Comment 36

> Username: Flamefire  
> Created_at: 2018-10-13 17:57:19 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429562195  

> Honestly, I never understood your argument that is_destroyed is broken.  
  
This is actually a discussion to be had in #110 so I'll answer it there. Please do reply on it as this is **essential**!  
  
> I won't dispute this. I've come to believe...  
  
Can we agree on either or both of:  
  
1. That there are cases where the order of destruction is **NOT** the inverse of construction  
2. A dead (destroyed) singleton is accessed in some situations  
  
The latter is actually a result from the former and that is caused by some strange things happening in shared libraries. I agree that this "strange thing" is the root cause and it is hard to solve. But maybe we can settle on handling the effects of this (the 2 things above)  
  
If you don't agree on those points please take a look at #111 which shows the problem. Play around with it if you like. For example I put printfs into the singleton ctor/dtor to print the address and saw that 1. is happening. Using valgrind showed that 2. is happening. Isn't this convincing evidence?  
  
My handling now consists of 2 parts:  
  
- Fix `is_destroyed` so that the tests from #110 pass  
- Let the runtime checks handle the case where a singleton is destroyed to soon. e.g. this: https://github.com/boostorg/serialization/blob/738695b70733f9d592a570fb17a505d6a029b48a/include/boost/archive/impl/archive_serializer_map.ipp#L54  
  - Bonus: Remove the assertions before those ifs as they WILL fire in that case  
  - This fixes the tests from #111

---

## Comment 37

> Username: robertramey  
> Created_at: 2018-10-13 18:22:23 UTC  
> Updated_at: 2018-10-13 18:23:25 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429564132  

Hmm - I looked at the test.  Right a way I have a problem with the following:  
  
`  
>  
int main(int argc, char**){  
   if(f() && g())  
   	return 0;  
 }  
`  
 What does the test return if the condition is false?

---

## Comment 38

> Username: robertramey  
> Created_at: 2018-10-13 18:37:18 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429565214  

Looking further, it looks to me that both g() and f() should always return false.  So the false conditional branch on the test would return ?  
  
And I think you've misunderstood the function of some of the api of the serialization library.  
  
> #define BOOST_SERIALIZATION_SOURCE  
  
 #include <boost/serialization/extended_type_info_typeid.hpp>  
  
 BOOST_SYMBOL_EXPORT int g(){  
   // Use a different(!) type  
   return 0 != boost::serialization::extended_type_info_typeid<float>::get_const_instance().get_key();  
 }  
  
should look more like:  
'  
class x {...}  
BOOST_SERIALIZATION_EXPORT_IMPLEMENT(x)  
BOOST_SERIALIZATION_EXPORT_KEY(x)  
  
BOOST_SYMBOL_EXPORT int g(){  
  return 0 != boost::serialization::extended_type_info_typeid<float>::get_const_instance().get_key();  
}  
'

---

## Comment 39

> Username: Flamefire  
> Created_at: 2018-10-13 20:02:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429570977  

Note: Better suited directly in #111:  
  
> What does the test return if the condition is false?  
  
Zero. This is defined for `int main` (and only for this). The only point for the check is to make sure the code is not optimized away. Can add an explicit return if wanted but wanted to keep the code small.  
  
About the export:     
Possibly misunderstood it, but I think it is correct: In the source files I want to export the function and in the main I want to import it. Hence the EXPORT/IMPORT on `g`/`h` (those are definitely correct)  
  
The `BOOST_SERIALIZATION_SOURCE` define might not be required though. I remember having it there because I used a `BOOST_SERIALIZATION_*` define which conditionally expanded to EXPORT/IMPORT. Can remove it.  
  
Where did you get the `class x` from? And `BOOST_SERIALIZATION_EXPORT_KEY` and `BOOST_SERIALIZATION_EXPORT_IMPLEMENT` don't exist(?)

---

## Comment 40

> Username: robertramey  
> Created_at: 2018-10-13 20:39:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429573530  

>Zero. This is defined for int main (and only for this). The only point for the check is to make sure the >code is not optimized away. Can add an explicit return if wanted but wanted to keep the code small.  
  
I seriously doubt that is true.  But no matter.  Your saying that main will return 0 regardless of the values of f() and g().  So how would this test fail?  
  
BOOST_CLASS_EXPORT_KEY  
BOOST_CLASS_EXPORT_IMPLEMENT  
...   
  
See documentation for explanation.  See test_dll_exported for an example  
  
class x is just a dummy class I made up on the sport for demo purposes.  
  
There's some confusion around the word EXPORT (and of course import).  In one context it's used to signify that some symbol created on one module is made "public" (EXPORT) so that some other module can find it (IMPORT).  
  
In the serialization library it's also used to indicate that a particular class type should be placed in a table (extended_type_info...) so that the type can later be found "find_key()" etc.  Since the entry is a type and a type is unique in the program, this type has to be wrapped into a "singleton" which is where our difficulties come from.  It's sort of "reflection" for types that we conjured up.  It's overly complex - but it was the only way I could figure out how to do it.  This was needed in the effort to support non-intrusive serialization of derived types.  That is, you need a table to map a point to an abstract base class to the derived class so on loading you create the right thing.  Microsoft MFC serialization doesn't have this problem because it doesn't serialization non-intrusively so it can't serialize "imported" types like stl data structures.  Cerial doesn't need this because it doesn't support serialization of raw pointers  much less abstract ones.  They're good systems - but not a comprehensive ones.  
  
I should say I believe that my version of your patch is equivalent to your original one - without which I couldn't have created it.  That is, I believe that your patch included some stuff that wasn't strictly necessary and I believe that testing will support that believe.  
  
But we're not totally out of the woods yet.  As I said, test_dll_export still fails with (and only with) certain clang compilers.  But it fails with an explicit error message so I believe that this is a different (though perhaps related) problem.  It would be fairly easy to track down and I have a good debugger.  I use the clang compiler on osx which is the environment where it fails.  But damned if doesn't pass on my machine !!!.  I'm using a recent clang(8) and the one in the test matrix is later(10?).  Hopefully soon I'll be able to track this down.

---

## Comment 41

> Username: davydden  
> Created_at: 2018-10-14 04:59:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429596833  

@robertramey to be frank, for my experience with open source projects on github I expected that the following would happen for this pull request (PR)  
- You review the changes, namely...  
- click on the the “Files changed” tab at the top of the page and leave comments on specific parts of suggested code changes, importantly engage in the discussion **before** any changes are made  
- this is a convenient discussion mechanism as you can leave comment to specific line of changes (additions or removal)  
- maybe the author misunderstand some parts of boost  
- maybe you misunderstand his intention  
- either way, at the end end of the day, **in the dialogue** with the community we arrive at the state that satisfies everyone and hopefully fixes the annoying issue, important for many downstream projects.  
- the modified (if necessary) PR gets tested again, merged, **the author gets credit** by being the commit author that is merge upstream.  
  
What happened instead is that based on this PR you created your set of changes. Post-factum you say your version is equivalent to this one. If there is really a need to rewrite the author’s PR and clean it up, you could have created a separate PR that is meant to “supersede” this one. Then you and @Flamefire (and the community as a whole) could have had **a dialogue before** changes are made (I.e. “this is your set of changes, this is mine set of changes for the same issue, which one is better?”). You would have had a chance to compare the two, and improve further the code by getting feedback from the community. But this did not happen either.   
  
The end result in terms of code changes might be the same, but the way to get there is very different IMO.

---

## Comment 42

> Username: Flamefire  
> Created_at: 2018-10-14 08:00:55 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429604959  

> I seriously doubt that is true. But no matter. Your saying that main will return 0 regardless of the values of f() and g(). So how would this test fail?  
  
You may check the C++ standard: https://stackoverflow.com/a/19293663/1930508. However I added new commits to #111 to make this explicit and not confuse people not knowing the speciality of `main`.  
  
This test does not simply fail. As explained in that PR it crashes which is what I'm talking about: `*** Error in `bin.v2/libs/serialization/test/test_multi_shared_lib.test/gcc-7/debug/link-static/test_multi_shared_lib': double free or corruption (fasttop): 0x000000000088fc20 ***`  
  
Thanks for your explanation on `BOOST_CLASS_EXPORT_KEY` (seems you confused the names earlier?) but that is irrelevant for #111. No classes are exportet, there are simply 2 functions implemented in 2 shared libraries which are called from the main executable. All classes (2 singleton) are from boost.Serialization which are instantiated directly to show the bug (same would occur if you serialize/create serialization code for those 2 types (float, double))  
  
> I should say I believe that my version of your patch is equivalent to your original one - without which I couldn't have created it. That is, I believe that your patch included some stuff that wasn't strictly necessary and I believe that testing will support that believe.  
  
This is wrong. Your patch doesn't include the fix for `is_destroyed`, keeps inconsistent and plain wrong comments and additionally removes the feature that singleton classes with protected constructors are allowed (now they would fail to compile)     
Testing: You didn't review the tests I made and did not merge them. Those tests show that those bugs are still there, see #110 and #111 (CI is still running after I rebased them)  
  
I will rebase this PR on current dev but will overwrite your changes. What you did is dangerous again: Your `get_singleton_module` function may be eliminated by optimization in shared libs. This could have been pointed out by review...  
  
### This will be my last effort on this unless  
  
- You properly review the test PRs #127 (factored out from #110), #110 and #111 and merge them once done. Failure in develop is acceptable so further PRs (e.g. this one) can fix them  
  - Those PRs are intentionally made as small as possible.  
- You properly review this PR  
  
What is happening here is **not** how Open Source projects should work. Thanks @davydden for summarizing it. Exactly my point!

---

## Comment 43

> Username: tobias-loew  
> Created_at: 2018-10-14 09:31:17 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429609958  

@davydden, @Flamefire  
  
I'd also like to be frank:  
The boost-libraries is not another github open source project: it has been there long before github or even git was created.   
Boost-libraries stand for highest-quality, peer-reviewed code. They are used world-wide in many different projects, ranging from little toy-projects up to crucial production code in companies and public institutions, where a lot of money or even lives depend on its correctness.  
The boost-community decided that once a library is part of boost, the library authors (resp. owners) are responsible for its maintenance: they are the instance you have to convince that your PR should be merged into the code. So, they have a lot of power, but on the other side, it's their heads that got chopped off if something gets broken. So in a way, they're the last line of defense between order and PR-chaos.  
Coming now to this specific problem: I learned that there are certain problems with the destruction order on linux-systems with dynamically loaded modules. You tried to fix it and came up with a solution that worked for you and the maintainer merged the changes into the public release. Later on it showed up that the fix broke other people's code (just look into the boost-dev archive), and that definitely shouldn't happen.  
The current state of the PR looks ok for me, but I still got two issues with it:  
-  Even though construction/destruction of static variables is all what this PR is about, the debug and release versions differ here a lot. So the debug version is only of limited value for analyses.   
- Since all the changes result from faults only present on linux-system (IMHO from a C++ perspective the original pre 1.63 code is absolutely correct), I really would like to see a comment on that or the usage of target-specific macros. Otherwise, I expect, some years from now, other coders will "clean up" code and reintroduce the linux problem.  
  
The only functional difference I can see, is that the PR-code forces is_destroyed to true when the singleton_wrapper gets destructed. If this is the crucial change then why isn't there any comment in the code? Please, additionally, try to explain the code with code-comments.  
  
An a final please: let's not draw red lines - we're all better than that.

---

## Comment 44

> Username: Flamefire  
> Created_at: 2018-10-14 09:50:19 UTC  
> Updated_at: 2018-10-14 10:01:34 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429611134  

@tobias-loew Thank you. Your points are valuable as they ask real questions about the code at hand.  
  
> So the debug version is only of limited value for analyses.  
  
For this there is #127 which tests release versions on CI. Is this what you meant?  
  
> I really would like to see a comment on that or the usage of target-specific macros.  
  
There are none due to the following reason: I acknowledge that there ARE situations where a singleton is destroyed although it shouldn't be. The reasons for this are unclear (although they happen only on linux, and they may be solved, or not... they are hard at least). So I added tests for `is_destroyed` and destructors accessing singletons check this before accessing the singleton. This solves the problem in all cases without resorting to target-dependent macros (What if another platform behaves like Linux? Is this solution incorrect for other platforms? I believe not)     
  
> original pre 1.63 code is absolutely correct  
  
Did you mean pre 1.65? If yes, I mostly agree. There the `is_destroyed` function was working and it was checked (it still is) as it should. Only "Bug" is the assert above the runtime check which might make people want to remove this.  
  
> The only functional difference I can see, is that the PR-code forces is_destroyed to true when the singleton_wrapper gets destructed. If this is the crucial change then why isn't there any comment in the code?  
  
Is there really a need to explain by comments what `is_destroyed` is supposed to do? See https://github.com/boostorg/serialization/pull/105/files#diff-3f1e861be15fd4926f5e5b00388f2309R134     
What would you explain there? (Real question, IMO its self-explanatory but your question suggests this to be not true)     
Edit: Added more comments: https://github.com/boostorg/serialization/pull/105/commits/89aec9a5b6bedb11e5a8c990e4ec4fcbb5fcdeba  
  
>  Please, additionally, try to explain the code with code-comments.  
  
I Did. See the most important parts here:  
  
-  https://github.com/boostorg/serialization/pull/105/files#diff-3f1e861be15fd4926f5e5b00388f2309R84  
- https://github.com/boostorg/serialization/pull/105/files#diff-3f1e861be15fd4926f5e5b00388f2309R120  
- https://github.com/boostorg/serialization/pull/105/files#diff-3f1e861be15fd4926f5e5b00388f2309R153  
- https://github.com/boostorg/serialization/pull/105/files#diff-3f1e861be15fd4926f5e5b00388f2309R169  
  
What needs more explanation? Could you comment this on patch lines?  
  
> Otherwise, I expect, some years from now, other coders will "clean up" code and reintroduce the linux problem.  
  
This is why **everything** (here) is accompanied by tests showing RED before and GREEN after this PR.  
  
Finally:  
  
> Boost-libraries stand for highest-quality, peer-reviewed code.  
  
If the maintainer pushes own changes without even giving anyone the chance to review/comment it, the value of this is reduced. Yes it ultimately comes down to the maintainer what gets in, but if it is **only** the maintainer and his opinion that matters, then it is no longer "peer-reviewed".

---

## Comment 45

> Username: vinniefalco  
> Created_at: 2018-10-14 13:31:10 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429626676  

"The dream" for a library author is for enthusiastic community contributors to step forward and first assist, then take over maintenance for the library in a fashion that is as consistent, good, or better than the level of quality sustained by the original author. This can never happen without cultivating their interest through engagement, mentoring, and being generous with credit.

---

## Comment 46

> Username: robertramey  
> Created_at: 2018-10-14 16:28:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429640534  

>@robertramey to be frank, for my experience with open source projects on github I expected that the following would happen for this pull request (PR)  
  
I don't doubt your good intentions here.  But I think your expectations are unrealistic.  you seem to picture the "open source development" process as some sort of cooperative venture among equals.  It is not and can never be.  The main issue is who takes responsibility for delivering a quality product.  Responsibility cannot be assigned or delegated to an amorphous group.  When something goes wrong - someone has to be tasked with getting it fixed.  If this "someone" is a group of people, there is no guarantee that this "assignment/delegation" can be accomplished.  This not a comment on open source development, GitHub or boost, It's a long held and agreed upon feature of effective human collaboration.  This is why we have presidents, ceos, team captains and ... software product managers.  
  
In the context of Github/Boost, this function is fulfilled by the library author or maintainer.  His job is to act as a "gate keeper" to accept only the best improvements.  Along with this responsibility he as the authority to decide what to accept, delegate tasks to other people, reverse those decisions when necessary.  When something goes wrong he, and only he, takes the blame.  Responsibility cannot be delegated.  This is unlike authority (to fix or test or whatever) which can be delegated.  If I accept a less than optimal fix, it's my problem.  I couldn't just point the finger at contributor X and assign blame to him. It also means that inevitable trade offs are made by a single person, in this case me.  Of course every time such a tradeoff is made, someone will disagree and that's unfortunate.  But that is the nature of the word "tradeoff".  But by keeping this authority in the hands of one person, it's much more likely that the work (in this case the serialization library) will maintain a logical coherence and singularity of purpose which has been essential for keeping it relevant and useful for these many years.   
  
Note that in the course of discussion of this issue and in the past, I've suggested that anyone with interest could peel off the singleton and make it a new boost library and submit it to boost. This makes a lot of sense as the issues related to a singleton are a lot more subtle than meets the eye and the chances of a casual user getting it right are very small.  This is especially true in presence of issues related to order of static initialization, DLLS, visibility No one has shown any interest in undertaking such a task and accepting responsibility for it.  about 15 years ago such a library was submitted to boost by a very highly regarded author of other boost libraries.  The submission was reject for some reason that I can't remember.  Faced with this, I made my own - and here we are.  
  
The PR system is very helpful.  But the way it's setup it just permits me to merge the PR into the branch before I can test it.  And many times the PR has some useful parts, but has some other parts besides.  I choose to handle this in the way I have.  This is based solely on expediency - nothing more. Note that I make these changes in the develop branch so that I can watch the results on the Boost Test matrix - which is much more comprehensive than the CI.  Also not that all changes to the develop branch are public and considered provisional.  So the door is still open to those who might feel that I got something wrong.  
  
I try to give credit to everyone who makes a useful contribution.  I don't think I've been remiss in that in this instance.  Some PRs are misguided, incomplete, go beyond appropriate scope, not well tested, cannot be verified, break something else, narrow the applicability of the library, or suffer from some other problem so they can't be merged without change.  Only the most trivial can be merged without change.  In this particular case, my motivation for the changes I did rather than just accepting the PR should be pretty obvious by just comparing the actual changes the proposed ones.  
  
I think your comment greatly underestimates the time, effort and commitment I or any author of a boost library invests to keep improving this library.  You don't see the work involved in reviewing PRs which suffer from the problems above. The approach you suggest would greatly increase that requirements of that commitment while diminishing the quality of the library (such as it is).

---

## Comment 47

> Username: davydden  
> Created_at: 2018-10-14 17:10:33 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-429643731  

@robertramey   
  
> some sort of cooperative venture among equals  
  
Perhaps my wording was not precise, but i never implied that. I also never implied that the responsibility is shared among an amorphous group. All what you say is perfectly fine and I don't disagree with that.  
  
My main concern was the workflow of PRs, i.e. discussion on specific issues/requests @tobias-loew and @Flamefire have above. This does **not** imply that you and @Flamefire are equal in decision taking/responsibility/etc.  
  
> I think your comment greatly underestimates the time, effort and commitment I or any author of a boost library invests to keep improving this library.  
  
I apologies if this sounded so, that was never my intention. I was commenting on the workflow only.  
At the end of the day, it's your decision, nobody argues about this. I think you, as an author and maintainer of the library, can get more in return by following the other approach. But again, different strokes for different folks.  
  
But let's not dive into this topic. I will refrain from making further comments on this PR, as those are not related to the suggested changes. I hope that you, @Flamefire and @tobias-loew will continue discussing technical aspects of this PR.  
  
p.s. disclaimer: I never met @Flamefire on-line or off-line, so my comments here don't have any personal bias towards either sides (author or maintainer). The only reason I watch this PR is because this issue affects other projects.

---

## Review 48 [Commented]

> Username: pdimov  
> Created_at: 2018-10-16 14:45:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/105#pullrequestreview-165200455  

📁 test/test_singleton.cpp

```diff
  11 |- #include <iostream>
  12 |- #include <boost/serialization/singleton.hpp>
  13 |+ // <gennadiy.rozental@tfn.com>
```

> Username: pdimov  
> Created_at: 2018-10-16 14:45:32 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225572104  

Why is this line here?

> Username: Flamefire  
> Created_at: 2018-10-16 14:50:35 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225574383  

This is from #110 where I reverted changes made to a test I also changed. As explained in #110 the changed test currently in develop does rather test the compiler than the library which is why I reverted/overwrote it.  
  
If this is objectionable I'll add my own version of `test_singleton.cpp` as `test_singleton2.cpp` or below into this file but that would mean redundant code.

> Username: pdimov  
> Created_at: 2018-10-16 15:05:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225581561  

What I don't understand is what's Gennadiy Rozental's e-mail doing here, on a line of its own, it makes no sense to me.  
  
Re test_singleton2.cpp, yes, I was going to suggest the same thing. I'd also drop the Travis changes, or just keep the essential changes to a minimum.  
  
I also wonder whether the test will be cleaner if it's split into two, one testing a plain singleton, the other - inherited. There will be duplication, sure, but the array of states and the base class will vanish, so it could be more readable that way.

> Username: Flamefire  
> Created_at: 2018-10-16 15:13:49 UTC  
> Updated_at: 2018-10-16 15:13:50 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225585035  

As mentioned I reverted the original commit: https://github.com/boostorg/serialization/commit/8ca532a4d5b43f9e468a1a2a36dfb489e9ecc422#diff-b2f7572da5c9081e523924931012ab08  
  
> Re test_singleton2.cpp, yes, I was going to suggest the same thing. I'd also drop the Travis changes, or just keep the essential changes to a minimum.  
  
Some are required (`-fPIC`), I can drop the rest, as they are in #105   
  
> I also wonder whether the test will be cleaner if it's split into two, one testing a plain singleton, the other - inherited. There will be duplication, sure, but the array of states and the base class will vanish, so it could be more readable that way.  
  
This would mean 2 separate compilation units. I wanted to have the 2 use cases (plain, inherited) next to each other, to show the similarity and difference. But I can split this in 2 files, you think, this is more readable.

> Username: pdimov  
> Created_at: 2018-10-16 16:37:58 UTC  
> Updated_at: 2018-10-16 16:37:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225618235  

What I didn't like about this patch is that it's not clear which changes are necessary to make the test pass, and what aren't. The singleton_module changes, for instance, and a few other things.  
  
But I now see that you're simply undoing https://github.com/boostorg/serialization/commit/e1893dd28c408bb25352596e2f91892b3d570164#diff-3f1e861be15fd4926f5e5b00388f2309.

> Username: Flamefire  
> Created_at: 2018-10-16 16:41:04 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225619384  

Yes got messy due to the failed attempt to fix this which is why I added an explicit revert commit. My Patch is a single commit here: https://github.com/boostorg/serialization/pull/105/commits/e0691b0af85b899a0bc9d9c4a94d763b2a25b003 (see the name)  
  
The changes there are much less and mostly comments. Also compare the pre-1.65 version which that patch is based on.

> Username: Flamefire  
> Created_at: 2018-10-17 07:56:10 UTC  
> Updated_at: 2018-10-17 07:56:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225815747  

As requested I split the `is_destroyed` test into 2: https://github.com/boostorg/serialization/pull/129


---

## Review 49 [Commented]

> Username: pdimov  
> Created_at: 2018-10-16 16:40:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/serialization/pull/105#pullrequestreview-165258632  

📁 include/boost/serialization/singleton.hpp

```diff
 135 |-         use(m_instance);
 170 |+         // the sequence of object initialization.
 171 |+         use(& m_instance);
```

> Username: pdimov  
> Created_at: 2018-10-16 16:40:04 UTC  
> Updated_at: 2018-10-16 16:40:05 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225619041  

`use(m_instance)` - as before this patch - is undefined behavior, but I think that this one is undefined too. Non-undefined would have been to use a pointer as m_instance. Of course getting this past Robert may prove problematic. :-)

> Username: Flamefire  
> Created_at: 2018-10-16 16:42:08 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225619762  

Can you explain why it is UB?  
  
I do think this line is not required because of https://github.com/boostorg/serialization/pull/105/files/53392794c49ba129c4f3eb70debb7426746b325b#diff-3f1e861be15fd4926f5e5b00388f2309R198 but I'm to afraid to remove it ;)

> Username: pdimov  
> Created_at: 2018-10-16 16:43:45 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225620298  

In either case, I don't see how this is going to work - `use` will just get inlined here and disappear. But who knows.

> Username: pdimov  
> Created_at: 2018-10-16 16:53:45 UTC  
> Updated_at: 2018-10-16 16:53:46 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225623702  

At the point the initializer for m_instance is called, m_instance is not yet initialized, and using a reference that does not refer to a valid object is undefined behavior.  
  
A pointer would not have this issue because globals are zero-initialized first, before the dynamic initializers are run, so the value of m_instance when accessed in its own initializer would be nullptr.  
  
Even better would probably be to use a pointer for m_instance and `use(&m_instance)`, which avoids all these subtleties, as the address of m_instance is always valid no matter whether it's initialized or not.

> Username: Flamefire  
> Created_at: 2018-10-17 07:05:07 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225800727  

Wait. My patch uses `&m_instance`, hence it takes the address of the object. This is never dereferenced. With your statement   
  
>  as the address of m_instance is always valid no matter whether it's initialized or not.  
  
This is ok, isn't it?  
  
So at no point a reference is used, we only get the address of a static member.

> Username: pdimov  
> Created_at: 2018-10-17 13:29:11 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225927773  

`m_instance` is an uninitialized reference. `&m_instance` is undefined in that case.

> Username: Flamefire  
> Created_at: 2018-10-17 15:18:14 UTC  
> Updated_at: 2018-10-17 15:18:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225977091  

Actually I don't understand WHY it is required. Just judging from the comment this is to "construct the instance at pre-execution time". But the `m_instance` with its initialization is there for exactly the same reason (I added this as a comment with a link to https://groups.google.com/forum/#!topic/microsoft.public.vc.language/kDVNLnIsfZk)   
  
But @robertramey wrote in an email:  
  
> LOL - it's only in there because the tests fail without it.  
  
I verified this locally (Linux, GCC), but don't understand it...  
  
I'm currently testing `static T * m_instance;` and it seems to work. But this would be a separate PR.

> Username: pdimov  
> Created_at: 2018-10-17 15:24:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#discussion_r225979653  

My guess would be that when there are no references to m_instance, and it's in a static library, it's not being linked at all.


---

## Comment 50

> Username: Flamefire  
> Created_at: 2018-11-08 06:53:52 UTC  
> Url: https://github.com/boostorg/serialization/pull/105#issuecomment-436891846  

Closed in favor of the simpler #131

---
