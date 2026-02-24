# #104 - Singleton destruction check completely broken leading to memory leaks or crashs [Closed]

> Username: Flamefire  
> Created at: 2018-04-25 15:21:33 UTC  
> Updated at: 2024-10-17 09:46:42 UTC  
> Closed at: 2018-12-05 08:22:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/104  

The commit https://github.com/boostorg/serialization/commit/b0a794da38b52c8ae0f8155cf3bb30321cf67f00 introduced a change that completely breaks the singleton implementation.  
  
The problem is: `singleton<T>` is NOT a singleton! It is NEVER instantiated. Instead it instantiates a class `singleton_wrapper` derived from T. That instance is then returned in the `get_instance` method.  
  
Because it is never instantiated it is also never destroyed. This leads to the `destroyed` flag never set. It only happens to be false because it is default (or value?) initialized.  
  
This was made even worse with commit https://github.com/boostorg/serialization/commit/7d216b47619f6b7aa57c5f0e48b9131a025426f9 where the singleton instance is not a static variable with automatic, thread-safe construction/destruction but a pointer which is leaked.  
  
The whole implementation is against the singleton pattern: Currently is is completely possible to create a `singleton<Foo>` instance. Even worse: Currently you would have to do this EXACTLY once to have correct code for EVERY `singleton`. If you don't do this you get a memory leak or corrupted memory depending on the version. If you do this more than once you yet a double-free error which cannot be caught.  
  
My suggestion: Make the singleton a real singleton (private ctor, no assignment etc.), inherit from T directly and let the instance variable be `singleton` instead of `singleton_wrapper` (get rid of that, but check with http://tinyurl.com/ljdp8 that this does not cause a regression, if it does, put the members ( `destroyed`...) into `singleton_wrapper` like before), check the setting of the `destroyed` flag which must rely on the actual destruction of the "real"(!!!) singleton instance.

---

## Comment 1

> Username: Flamefire  
> Created at: 2018-04-25 16:10:15 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-384343550  

And to echo it here a reproducer in text:  
  
- `extended_type_info_typeid` is used which causes a singleton to be created  
- It registers in the `ktmap` singleton (https://github.com/boostorg/serialization/blob/b73329fe45b02965c9f2059f7d90c285db1b3630/src/extended_type_info_typeid.cpp#L91)  
- On exit the `ktmap` singleton gets destroyed before the `extended_type_info_typeid` singleton. However `~singleton` is never called, because no instance exists. Only `~singleton_wrapper` is called.  
-  `extended_type_info_typeid` singleton gets destroyed and access `ktmap` in it dtor (https://github.com/boostorg/serialization/blob/b73329fe45b02965c9f2059f7d90c285db1b3630/src/extended_type_info_typeid.cpp#L99)  
- as `destroyed` is not set, it accesses dead memory  
  
The problem is very complicated to reproduce, due to unknown init/finalize order. I assume: `typeid`-singleton gets created and registers for destruction. It calls into `ktmap` which causes its instantiation and registers `ktmap` for destruction. Finalize order is the reverse so `ktmap` is destroyed first.  
  
So one problem is the usage of the `singleton` ctor/dtor which makes people assume it is used.

---

## Comment 2

> Username: Flamefire  
> Created at: 2018-04-26 13:40:04 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-384644471  

Testcase to demonstrate this: https://gist.github.com/Flamefire/80af5fcdc0f2a6787858738ce07b25bf  
  
Run with Boost 1.64 -> 1.67:  
  
```  
boost_1_64_0  
With assert:  
With print:  
~A<1>  
singleton<A<0> >::is_destroyed(): 0  
singleton<A<1> >::is_destroyed(): 1  
~A<0>  
singleton<A<0> >::is_destroyed(): 1  
singleton<A<1> >::is_destroyed(): 1  
  
boost_1_65_0  
With assert:  
terminate called after throwing an instance of 'std::runtime_error'  
  what():  Assertion failed: singleton<A<0> >::is_destroyed()  
Abgebrochen (Speicherabzug geschrieben)  
With print:  
~A<1>  
singleton<A<0> >::is_destroyed(): 0  
singleton<A<1> >::is_destroyed(): 0  
~A<0>  
singleton<A<0> >::is_destroyed(): 0  
singleton<A<1> >::is_destroyed(): 0  
  
boost_1_65_1  
With assert:  
terminate called after throwing an instance of 'std::runtime_error'  
  what():  Assertion failed: singleton<A<0> >::is_destroyed()  
Abgebrochen (Speicherabzug geschrieben)  
With print:  
~A<1>  
singleton<A<0> >::is_destroyed(): 0  
singleton<A<1> >::is_destroyed(): 0  
~A<0>  
singleton<A<0> >::is_destroyed(): 0  
singleton<A<1> >::is_destroyed(): 0  
  
boost_1_66_0  
With assert:  
terminate called after throwing an instance of 'std::runtime_error'  
  what():  Assertion failed: states[0] == CS_DESTROYED  
Abgebrochen (Speicherabzug geschrieben)  
With print:  
  
boost_1_67_0  
With assert:  
terminate called after throwing an instance of 'std::runtime_error'  
  what():  Assertion failed: states[0] == CS_DESTROYED  
Abgebrochen (Speicherabzug geschrieben)  
With print:  
```  
  
As you can easily see 1.64 is working, 1.65.0 introduced the bug that `is_destroyed` is never set and 1.66 and up never destroys the instance.

---

## Comment 3

> Username: Flamefire  
> Created at: 2018-04-27 08:38:05 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-384905015  

Some more analysis: `singleton<T>` is used in 2 ways: Once can inherit from it and use `T::get_const_instance()` or not change `T` and use `singleton<T>::get_const_instance()`  
  
The problem is with the latter as `singleton<T>` is in this case never instantiated while it is in the first case (through `T`). This is why the code breaks sometimes but not with every use.

---

## Comment 4

> Username: Flamefire  
> Created at: 2018-04-27 09:01:55 UTC  
> Updated at: 2018-04-27 11:57:00 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-384910968  

After some more analysis: I wonder what really happened.  
  
 `extended_type_info_typeid` refered to `singleton<tkmap>` in its ctor. This means that `singleton<tkmap>` has to be constructed first. Which means that it should be destructed last (see https://stackoverflow.com/questions/335369/finding-c-static-initialization-order-problems#335746)  
  
Apparently (maybe a bug in the compiler?) `singleton<tkmap>` is destructed first. This leads to the assertion failure `Assertion failed: (! singleton<tkmap>::is_destroyed()), function type_unregister, file libs/serialization/src/extended_type_info_typeid.cpp, line 98.` as reported by @vasild in #69. However that assertion failure is ok as there is a check for the same below in which case the unregister is skipped. The bug introduced does not set `is_destroyed` and hence it crashes ( `singleton<tkmap>` is still destructed first!)  
  
To summarize: A crash/valgrind detected memory error can occur, IF the compiler destroys `singleton<tkmap>` first. I read that the order of destruction is the order of the finished ctors. This would preclude the "bad" scenario. But it could be the order of *started* ctors. If both are valid in C++ then the occasional crash depending on compiler version is explained.  
  
Local Static function member is explained here: https://isocpp.org/wiki/faq/ctors#construct-on-first-use-v2. However I did not find anything about dtor order for this case.

---

## Comment 5

> Username: jalegido  
> Created at: 2018-04-27 10:43:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-384934628  

According to Microsoft (https://msdn.microsoft.com/en-us/library/xwec471e.aspx) msvc creates and destroys static objects in the same order they are in the code. In my experience, this means that it deppends of the order files in cl.exe.  
For example, static objects in A.cpp are initialezed before than objects in B.cpp if you exec "cl A.cpp B.cpp". But if you change the order "cl B.cpp A.cpp", then the initialization order also reverse.

---

## Comment 6

> Username: Flamefire  
> Created at: 2018-04-27 11:56:13 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-384948809  

This does not apply here. We don't have static objects, we have member function local static objects. This is different. According to this (https://stackoverflow.com/questions/335369/finding-c-static-initialization-order-problems#335746) it should work.   
  
IsoCPP FAQ does mention this too: https://isocpp.org/wiki/faq/ctors#construct-on-first-use-v2   
  
However I did not find anything about dtor order for this case.

---

## Comment 7

> Username: Flamefire  
> Created at: 2018-04-27 13:34:57 UTC  
> Updated at: 2018-04-27 14:07:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-384971535  

The problem occurs when using multiple shared libraries. It seems like this messes up the destruction order.  
  
Reproduction: https://gist.github.com/Flamefire/286e9e0e501731a04f10786450d3e711  
  
Some debug info by hooking the ctor/dtor of the singletons:  
  
> ctor 0x7f9f0aa074a0 std::multiset<const boost::serialization::typeid_system::extended_type_info_typeid_0*, boost::serialization::typeid_system::type_compare>  
> ctor 0x7f9f0a7f63e0 boost::serialization::extended_type_info_typeid<float>  
>   
> ctor 0x7f9f0a7f64a0 std::multiset<const boost::serialization::typeid_system::extended_type_info_typeid_0*, boost::serialization::typeid_system::type_compare>  
> ctor 0x7f9f0aa073e0 boost::serialization::extended_type_info_typeid<int>  
>   
> dtor 0x7f9f0aa073e0 boost::serialization::extended_type_info_typeid<int>  
> dtor 0x7f9f0aa074a0 std::multiset<const boost::serialization::typeid_system::extended_type_info_typeid_0*, boost::serialization::typeid_system::type_compare>  
> dtor 0x7f9f0a7f64a0 std::multiset<const boost::serialization::typeid_system::extended_type_info_typeid_0*, boost::serialization::typeid_system::type_compare>  
> dtor 0x7f9f0a7f63e0 boost::serialization::extended_type_info_typeid<float>  
  
Note how the 2 maps are destructed together which leads to the wrong ordering for the 2nd pair.

---

## Comment 8

> Username: robertramey  
> Created at: 2018-04-28 21:32:59 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-385206941  

OK - I'm working on this now.  I'm presuming that https://gist.github.com/Flamefire/286e9e0e501731a04f10786450d3e711 contains the definitive test case.   I can see that it was a bitch to figure this out.  I would like to figure out a way to included in the test suite - but there doesn't seem to be a way to do this. Making two DLLS etc. ...

---

## Comment 9

> Username: robertramey  
> Created at: 2018-04-28 22:54:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-385210832  

we have two test cases here.  Are they both useful?  Or is it only the second one?

---

## Comment 10

> Username: Flamefire  
> Created at: 2018-04-29 12:51:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-385249391  

I found a way to include it in the test suite and opened PR #105 for that and the fix.  
  
The 2 test cases test something different: The 2nd (https://gist.github.com/Flamefire/286e9e0e501731a04f10786450d3e711) tests the library from a higher level (just minimal code to break it)  
  
The other one tests the implementation of the singleton thoroughly (e.g. checking that they are in fact destructed and set the flag)

---

## Comment 11

> Username: kunshouout  
> Created at: 2018-05-09 18:38:08 UTC  
> Updated at: 2018-05-09 18:38:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-387835322  

I suffer from this issue recently. My code got memory leaks. Is there a quick fix for this problem. I am using vs2017 + boost 1.66.  
Thx

---

## Comment 12

> Username: Flamefire  
> Created at: 2018-05-09 19:18:57 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-387846721  

You can test my PR: https://github.com/boostorg/serialization/pull/105  
  
Either as a whole or just the fix (last 2 commits). Note that you can generate patches to download by appending ".diff" to the url on GitHub: https://github.com/boostorg/serialization/commit/17c952b7634b9c0ab8f257c679451587b5c7f280.diff and https://github.com/boostorg/serialization/commit/d63b8535f5e615c1edc8ab50078508e4d56816cf.diff

---

## Comment 13

> Username: res2k  
> Created at: 2018-08-28 09:12:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-416510471  

FWIW, the reason why `test_dll_exported` currently succeeds on Travis appears to be `--as-needed`: Ubuntu enables this flag by default, and that apparently is enough to change the initialization/finalization order such that everything works.

---

## Comment 14

> Username: res2k  
> Created at: 2018-08-28 10:38:10 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-416535952  

Small addendum to previous comment: `--as-needed` doesn't change the initialization/finalization, it causes the _removal_ of the reference to `libpolymorphic_derived2.so`. Defining something in that lib so it's actually needed at runtime – so the reference stays – causes the test to fail again: https://travis-ci.org/res2k/serialization/builds/421484746

---

## Comment 15

> Username: robertramey  
> Created at: 2018-08-28 15:05:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-416622195  

FYI - I spent a little time reviewing the test matrix for test_dll_export to try to find some commonality.  It seems that failures occur only on linux platforms.  Failures seem to be across all combinations of compiler and standard library implementations.  I'm still looking at this so my assessment could change.  It's a little tedious to do with our tools.

---

## Comment 16

> Username: Flamefire  
> Created at: 2018-08-28 16:43:19 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-416657395  

This matches my experiments with the crash as described here and shown in #111:  
It only affects linux but there for all environments. Looks like shared libraries are handled differently in linux in general.

---

## Comment 17

> Username: res2k  
> Created at: 2018-08-29 09:44:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-416892815  

I think I found the culprit: By default, the Linux dynamic linker binds dynamic symbols in a shared object to any already existing symbols in the global namespace. Some `singleton<>` instances are defined in both the executable and the `.so` (as it may happen if you use `singleton<>` in a shared header).  
When the singletons are used the instance in the executable is constructed; for some reason, their destruction is scheduled for after the `libboost_serialization` finalization, meaning it's singletons are already destroyed, resulting in funny things happening as they're being used by the singletons related to the `polymorphic` classes.  
One way to change this behaviour is to pass `-Bsymbolic -Bsymbolic-functions` to the linker; doing so fixes the crash. It might also possible obtain the desired behaviour by using `__attribute__((visibility(…)))`, I'm trying to see if I can get anywhere with that.

---

## Comment 18

> Username: res2k  
> Created at: 2018-08-29 13:14:18 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-416948110  

It's possible to fix the issue by fiddling with `__attribute__((visibility(…)))`, see https://gist.github.com/res2k/c5a9e90bb2f22d0aea0b9f8cde6f1ef4 for the changes I had to make.

---

## Comment 19

> Username: res2k  
> Created at: 2018-08-30 07:10:03 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-417213451  

Changes, cleaned up, and CI results: https://github.com/res2k/serialization/pull/10

---

## Comment 20

> Username: Flamefire  
> Created at: 2018-08-30 09:06:14 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-417246388  

Thanks!  
What I'm missing in your explanation: There are definitely 2 instances of the same singleton for shared libraries linked to static boost and they are destroyed just after each other instead of their correct, library intern order which leads to the crash in the 2nd library.  
  
Did you also test #111? Would be interesting to know if that fixes it too.  
  
And finally: Of course the singleton bug has to be fixed anyway: #110

---

## Comment 21

> Username: krojew  
> Created at: 2018-09-03 06:52:55 UTC  
> Updated at: 2018-09-03 06:53:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-418017771  

I am running into crashes with boost::serialization::singleton in 1.68; probably related to this issue. Example valgrind dump:  
  
```  
 ==2665== Invalid read of size 8  
==2665==    at 0xA63DB81: boost::serialization::typeid_system::extended_type_info_typeid_0::type_unregister() (in libboost_serialization.so.1.68.0)  
==2665==    by 0x7BD40D: boost::serialization::extended_type_info_typeid<std::unordered_set<long, std::hash<long>, std::equal_to<long>, std::allocator<long> > >::~extended_type_info_typeid() (extended_type_info_typeid.hpp:96)  
==2665==    by 0x7BCFC4: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::unordered_set<long, std::hash<long>, std::equal_to<long>, std::allocator<long> > > >::get_instance()::singleton_wrapper::~singleton_wrapper() (singleton.hpp:121)  
==2665==    by 0xC3D3040: __run_exit_handlers (exit.c:108)  
==2665==    by 0xC3D3139: exit (exit.c:139)  
==2665==    by 0xC3B1B9D: (below main) (libc-start.c:344)  
==2665==  Address 0x12bfc820 is 32 bytes inside a block of size 40 free'd  
==2665==    at 0x4C3123B: operator delete(void*) (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)  
==2665==    by 0xA63E1A7: boost::serialization::singleton<std::multiset<boost::serialization::typeid_system::extended_type_info_typeid_0 const*, boost::serialization::typeid_system::type_compare, std::allocator<boost::serialization::typeid_system::extended_type_info_typeid_0 const*> > >::get_instance()::singleton_wrapper::~singleton_wrapper() (in libboost_serialization.so.1.68.0)  
==2665==    by 0xC3D3614: __cxa_finalize (cxa_finalize.c:83)  
==2665==    by 0xA6355F2: ??? (in libboost_serialization.so.1.68.0)  
==2665==    by 0x4010B72: _dl_fini (dl-fini.c:138)  
==2665==    by 0xC3D3040: __run_exit_handlers (exit.c:108)  
==2665==    by 0xC3D3139: exit (exit.c:139)  
==2665==    by 0xC3B1B9D: (below main) (libc-start.c:344)  
==2665==  Block was alloc'd at  
==2665==    at 0x4C3017F: operator new(unsigned long) (in /usr/lib/valgrind/vgpreload_memcheck-amd64-linux.so)  
==2665==    by 0xA63D9F8: boost::serialization::typeid_system::extended_type_info_typeid_0::type_register(std::type_info const&) (in libboost_serialization.so.1.68.0)  
==2665==    by 0x7BD053: boost::serialization::extended_type_info_typeid<std::unordered_set<long, std::hash<long>, std::equal_to<long>, std::allocator<long> > >::extended_type_info_typeid() (extended_type_info_typeid.hpp:91)  
==2665==    by 0x7BCF8E: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::unordered_set<long, std::hash<long>, std::equal_to<long>, std::allocator<long> > > >::get_instance()::singleton_wrapper::singleton_wrapper() (singleton.hpp:121)  
==2665==    by 0x74DC99: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::unordered_set<long, std::hash<long>, std::equal_to<long>, std::allocator<long> > > >::get_instance() (singleton.hpp:122)  
==2665==    by 0x7BB248: boost::serialization::singleton<boost::serialization::extended_type_info_typeid<std::unordered_set<long, std::hash<long>, std::equal_to<long>, std::allocator<long> > > >::get_const_instance() (singleton.hpp:148)  
==2665==    by 0x50D095F: boost::archive::detail::iserializer<boost::archive::binary_iarchive, std::unordered_set<long, std::hash<long>, std::equal_to<long>, std::allocator<long> > >::iserializer() (iserializer.hpp:130)  
==2665==    by 0x50D08CE: boost::serialization::singleton<boost::archive::detail::iserializer<boost::archive::binary_iarchive, std::unordered_set<long, std::hash<long>, std::equal_to<long>, std::allocator<long> > > >::get_instance()::singleton_wrapper::singleton_wrapper() (singleton.hpp:121)  
==2665==    by 0x50898E8: boost::serialization::singleton<boost::archive::detail::iserializer<boost::archive::binary_iarchive, std::unordered_set<long, std::hash<long>, std::equal_to<long>, std::allocator<long> > > >::get_instance() (singleton.hpp:122)  
==2665==    by 0x5072DD8: __cxx_global_var_init.35 (singleton.hpp:162)  
==2665==    by 0x4010732: call_init (dl-init.c:72)  
==2665==    by 0x4010732: _dl_init (dl-init.c:119)  
==2665==    by 0x40010C9: ??? (in /lib/x86_64-linux-gnu/ld-2.27.so)  
```

---

## Comment 22

> Username: Flamefire  
> Created at: 2018-09-03 06:59:53 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-418019237  

Yes this is exactly this issue. You can manually apply #105 to fix that.

---

## Comment 23

> Username: res2k  
> Created at: 2018-09-03 14:10:38 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-418126648  

I made further changes related to ELF visibility (still at https://github.com/res2k/serialization/pull/10); the test cases from #110 and #111 now run successfully.  
  
@robertramey: Your feedback would be greatly appreciated.

---

## Comment 24

> Username: robertramey  
> Created at: 2018-09-03 14:41:07 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-418134660  

I'm currently bogged down in other stuff I can't set aside.  My experience is that once I get into something like this it ends up consuming much more time than one would think. But, I've been following this discussion and appreciate the contributions and efforts of all parties.  As I've said before this seems to me something related to unstandardized and differently implemented features of the C/C++ programming surface:dynamic loading and symbol visibility.  
  
It's seems you (everyone) is making progress.  I think it would be good if these results could be boiled down to a couple of tests which I could add to the permanent test suite.  Then we would see the results across the whole test matrix.  The test matrix is not as diverse as I would hope and it doesn't make it easy to review results by attributes like 32 vs 64 bit, std version, compiler version, os version, etc.  But it's the best we have and it's the best we have in a situation like this.  
  
Soooo ... - I would like to see this boiled down to one or two test.  Before doing this, please look carefully at the current tests.  The are set up so that, if appropriate, the same test can be run with different archive types.  The use "lightweight test" macros and follow the same general organization.  This makes it easier for me and others to understand.  This might not seem important but it is.  Keep in mind that the serialization library testing is designed to be a running history of all the failures we've ever found. Once a failure is found, we add it to the permanent test suite so the the effort cannot creep back into the code.  It also avoids playing "whack-a-mole" where by fixing problem1 creates problem2 and vice-versa resulting in an infinite "ping-pong".  After 15 years, the boost serialization is better than it's ever been and still getting better.  This is in large part due to the efforts of all of you and and others. It's much appreciated.

---

## Comment 25

> Username: Flamefire  
> Created at: 2018-09-03 14:51:24 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-418137110  

> I think it would be good if these results could be boiled down to a couple of tests which I could add to the permanent test suite.   
  
I already did that: #110 and #111     
Those PRs are just 2 tests (1 each) showing the bug in Boost.Serialization (#110) and the issue with shared libs which is caused by whatever (#111). They fail exactly as expected and described.  
  
#105 consists of those 2 PRs and 2 commits fixing the issue. I did it this way to decompose this (admittedly difficult) issue into its pieces so understanding the tests and changes is as easy as possible.

---

## Comment 26

> Username: res2k  
> Created at: 2018-09-03 15:44:22 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-418148928  

Though, 5d3d71424a043b720648e7a99af2a1f3f3f67d01 (the commit that makes #105 "work") merely removes the assertions that cause the test failures. Keep the assertions, they fire - the destruction order is still not quite "correct" there.

---

## Comment 27

> Username: Flamefire  
> Created at: 2018-09-03 15:57:02 UTC  
> Updated at: 2018-09-03 15:57:12 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-418151382  

Correct. This is IMO the only commit that requires a bit discussion as the rest is straight forward. My approach was: "We cannot fix the underlying issue as it is (C++) runtime related" (you seem to have fixed that though, but the amount of additional macros and their different use cases scare me... Maybe something was forgotten?)     
So after fixing the `singleton::is_destroyed` check I went for the simpler solution: Treat it as a simple pointer-is-valid check. If you look at those places you'll see that they are all like:  
  
```  
BOOST_ASSERT(global_ptr);  
if(global_ptr)  
  global_ptr->access(...);  
```  
  
So the assert isn't really required. And it makes sense to: What is done in this code is unregistering stuff that the calling class has registered. If the registry is already destroyed then there is no point in trying to unregister something.  
  
If those visibility macros work and are accepted, then this commit can be reverted though but the checks below should be kept. This way the assert is only a development aid to catch missing visibility stuff and the runtime check catches those unexpected cases so they don't crash the application. An explanation should also be added to the assert so no one will go and remove the check.

---

## Comment 28

> Username: res2k  
> Created at: 2018-09-03 16:35:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-418158219  

One thing I'm not so sure about is whether the "pointer check" approach plays well with dynamically loaded SOs using Boost.Serialization. The ELF symbol overriding may foul up such dynamic loading and unloading. (There's a test case, but unfortunately is broken.)  
  
Note that "lots of macros" isn't the only approach to reign in ELF symbol overriding. As I tried earlier, the `-Bsymbolic -Bsymbolic-functions` linker options also yield the desired behavior. Or, if the `-fvisibility` compiler option(s) is used, the amount of explicit visibility attributes could probably be reduced.  
Thing is just that these options require the _library client_ to link anything using Boost.Serialization with the right flags. But I chose the approach to have everything in the headers as that is transparent to the client.

---

## Comment 29

> Username: Flamefire  
> Created at: 2018-09-03 16:43:27 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-418159445  

> One thing I'm not so sure about is whether the "pointer check" approach plays well with dynamically loaded SOs using Boost.Serialization.   
  
I doubt that this can be a problem: We check a value in the same class we are gonna call. Even if something would be mixed up, we'll still get the desired behaviour: Call the class when its valid.

---

## Comment 30

> Username: Flamefire  
> Created at: 2018-12-05 08:22:34 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-444398824  

Fixed by https://github.com/boostorg/serialization/commit/f297d80d6ef55ac66525320d0477c17806aa57cd which should be shown by tests from https://github.com/boostorg/serialization/commit/cc2a996384baddd7b45ee93a5644e3829e9e4e86

---

## Comment 31

> Username: Rseding91  
> Created at: 2024-05-23 18:44:21 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-2127815313  

I was wondering if anyone with enough knowledge about this specific issue could tell me if a crash we're seeing on-exit could be this issue. Specifically our game Factorio is getting 'random' crashes in the AMD graphics driver for AMD GPU users when exiting the game. The stack trace is always identical and goes through boost::serialization::singleton before crashing. A symbolized stack trace (to the best of our abilities) can be seen in this report: https://forums.factorio.com/113613  
  
Some information:  
  
* The crash seems to be rare - most AMD players have never had this crash  
* For the ones who have the crash, it's random, but keeps happening  
* Factorio does not use boost  
* The stack trace is always the same and crashes inside AMDs graphics driver inside boost  
* I've tried contacting AMD about it, but they have so far not been able to help except to say "yep it's crashing in our driver, we don't know why."  
* I have no idea what version of boost the AMD drivers are using

---

## Comment 32

> Username: Squall-Leonhart  
> Created at: 2024-10-17 09:45:52 UTC  
> Updated at: 2024-10-17 09:46:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/104#issuecomment-2419064709  

> I was wondering if anyone with enough knowledge about this specific issue could tell me if a crash we're seeing on-exit could be this issue. Specifically our game Factorio is getting 'random' crashes in the AMD graphics driver for AMD GPU users when exiting the game. The stack trace is always identical and goes through boost::serialization::singleton before crashing. A symbolized stack trace (to the best of our abilities) can be seen in this report: https://forums.factorio.com/113613  
  
Cemu users are seeing the same in 24.8.1 (Vulkan) and 24.9.1 (Vulkan and OpenGL)  
  
Wonder if AMD has unknowingly upgraded core driver components into a broken state.
