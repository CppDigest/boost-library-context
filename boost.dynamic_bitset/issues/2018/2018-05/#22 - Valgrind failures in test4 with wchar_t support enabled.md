# #22 - Valgrind failures in test4 with wchar_t support enabled [Closed]

> Username: jeking3  
> Created at: 2018-05-16 15:23:05 UTC  
> Updated at: 2018-07-04 12:06:50 UTC  
> Closed at: 2018-07-04 12:06:50 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/22  

I'm not sure if this is a false positive or not.  
  
Environment: Using docker build environment from https://github.com/boostorg/boost/pull/184  
If I build with valgrind I end up with some errors in test 4, for example:  
  
boost@2525d5b3fe47:/boost/libs/dynamic_bitset/test$ VALGRIND_OPTS=--error-exitcode=1 ../../../b2 toolset=gcc variant=debug testing.launcher=valgrind  
  
```  
==298== Conditional jump or move depends on uninitialised value(s)  
==298==    at 0x590F3FA: __wmemchr_avx2 (memchr-avx2.S:66)  
==298==    by 0x4EEC94C: std::codecvt<wchar_t, char, __mbstate_t>::do_out(__mbstate_t&, wchar_t const*, wchar_t const*, wchar_t const*&, char*, char*, char*&) const (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==298==    by 0x4F308A5: std::basic_filebuf<wchar_t, std::char_traits<wchar_t> >::_M_convert_to_external(wchar_t*, long) (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==298==    by 0x4F30D21: std::basic_filebuf<wchar_t, std::char_traits<wchar_t> >::overflow(unsigned int) (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==298==    by 0x4F30A44: std::basic_filebuf<wchar_t, std::char_traits<wchar_t> >::_M_terminate_output() (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==298==    by 0x4F34A60: std::basic_filebuf<wchar_t, std::char_traits<wchar_t> >::close() (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==298==    by 0x4F36940: std::basic_ofstream<wchar_t, std::char_traits<wchar_t> >::close() (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==298==    by 0x11F8C9: void bitset_test<boost::dynamic_bitset<unsigned char, std::allocator<unsigned char> > >::stream_inserter<std::basic_ofstream<wchar_t, std::char_traits<wchar_t> > >(boost::dynamic_bitset<unsigned char, std::allocator<unsigned char> > const&, std::basic_ofstream<wchar_t, std::char_traits<wchar_t> >&, char const*) (bitset_test.hpp:1217)  
==298==    by 0x115DC6: void run_test_cases<unsigned char>() (dyn_bitset_unit_tests4.cpp:147)  
==298==    by 0x112936: test_main(int, char**) (dyn_bitset_unit_tests4.cpp:326)  
==298==    by 0x113BD1: boost::minimal_test::caller::operator()() (minimal.hpp:111)  
==298==    by 0x14102E: boost::detail::function::function_obj_invoker0<boost::minimal_test::caller, int>::invoke(boost::detail::function::function_buffer&) (function_template.hpp:138)  
```  
  
and  
```  
==298== Conditional jump or move depends on uninitialised value(s)  
==298==    at 0x590F617: __wmemchr_avx2 (memchr-avx2.S:282)  
==298==    by 0x4EE8B54: std::basic_istream<wchar_t, std::char_traits<wchar_t> >& std::getline<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >(std::basic_istream<wchar_t, std::char_traits<wchar_t> >&, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >&, wchar_t) (in /usr/lib/x86_64-linux-gnu/libstdc++.so.6.0.25)  
==298==    by 0x12D6AE: void bitset_test<boost::dynamic_bitset<unsigned int, std::allocator<unsigned int> > >::stream_extractor<std::__cxx11::basic_istringstream<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> > >(boost::dynamic_bitset<unsigned int, std::allocator<unsigned int> >&, std::__cxx11::basic_istringstream<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >&, std::__cxx11::basic_string<wchar_t, std::char_traits<wchar_t>, std::allocator<wchar_t> >&) (bitset_test.hpp:1374)  
==298==    by 0x119CAD: void run_test_cases<unsigned int>() (dyn_bitset_unit_tests4.cpp:305)  
==298==    by 0x112940: test_main(int, char**) (dyn_bitset_unit_tests4.cpp:328)  
==298==    by 0x113BD1: boost::minimal_test::caller::operator()() (minimal.hpp:111)  
==298==    by 0x14102E: boost::detail::function::function_obj_invoker0<boost::minimal_test::caller, int>::invoke(boost::detail::function::function_buffer&) (function_template.hpp:138)  
==298==    by 0x11D7E6: boost::function0<int>::operator()() const (function_template.hpp:769)  
==298==    by 0x114216: int boost::detail::do_invoke<boost::shared_ptr<boost::detail::translator_holder_base>, boost::function<int ()> >(boost::shared_ptr<boost::detail::translator_holder_base> const&, boost::function<int ()> const&) (execution_monitor.ipp:285)  
==298==    by 0x10F60B: boost::execution_monitor::catch_signals(boost::function<int ()> const&) (execution_monitor.ipp:874)  
==298==    by 0x10F78F: boost::execution_monitor::execute(boost::function<int ()> const&) (execution_monitor.ipp:1213)  
==298==    by 0x11245D: main (minimal.hpp:130)  
```  
  
If I add define=BOOST_DYNAMIC_BITSET_NO_WCHAR_T_TESTS then the problems go away.  This looks like it might be an issue in libstdc++ with wchar_t support but I'm not sure.  For now the valgrind tests are going to run without wide character tests.  It's better than having none at all.

---

## Comment 1

> Username: jwakely  
> Created at: 2018-05-17 19:43:53 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/22#issuecomment-389986139  

It's more likely that your valgrind doesn't know about the optimized AVX implementation of `wmemchr` in your libc, and they are false positives.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-05-18 01:30:23 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/22#issuecomment-390066460  

Related:  
  
https://www.e-learn.cn/content/wangluowenzhang/415811

---

## Comment 3

> Username: jeking3  
> Created at: 2018-05-18 01:32:38 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/22#issuecomment-390066848  

Given that the builds also fail on Windows with iostream related issues I am going to suggest the issue is in dynamic_bitset or the unit test until proven otherwise.  If we can pass all our CI tests clean and we still see this in the docker environment with libstdc++ 7.3 (gcc-7.3) then we'll report it to valgrind.  
  
Curiously they don't seem to fail with valgrind 3.10 and an older compiler combination on travis builds.

---

## Comment 4

> Username: jeking3  
> Created at: 2018-07-04 12:06:50 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/22#issuecomment-402459640  

Issue is resolved with a newer valgrind.
