# #64 - Multiply defined symbols when building under cygwin/cygwin64, gcc 6.4 and 7.3 link=shared [Closed]

> Username: jeking3  
> Created at: 2018-07-19 10:40:43 UTC  
> Updated at: 2019-02-10 13:06:28 UTC  
> Closed at: 2019-02-10 13:06:28 UTC  
> Url: https://github.com/boostorg/regex/issues/64  

I am trying to build iostreams which links against regex dynamically in the Jamfile(s).   Only on cygwin and cygwin64, I get this during CI builds:  
```  
gcc.link.dll bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\libboost_regex-gcc64-x64-1_68.dll.a  
bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\instances.o:instances.cpp:(.text$_ZN5boost16re_detail_10680012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_14c_regex_traitsIcEEEEE4findEv[_ZN5boost16re_detail_10680012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_14c_regex_traitsIcEEEEE4findEv]+0x0): multiple definition of `boost::re_detail_106800::perl_matcher<char const*, std::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::c_regex_traits<char> > >::find()'  
bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\cregex.o:cregex.cpp:(.text+0x2db0): first defined here  
bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\instances.o:instances.cpp:(.text$_ZN5boost16re_detail_10680012perl_matcherIN9__gnu_cxx17__normal_iteratorIPKcSsEESaINS_9sub_matchIS6_EEENS_12regex_traitsIcNS_14c_regex_traitsIcEEEEE4findEv[_ZN5boost16re_detail_10680012perl_matcherIN9__gnu_cxx17__normal_iteratorIPKcSsEESaINS_9sub_matchIS6_EEENS_12regex_traitsIcNS_14c_regex_traitsIcEEEEE4findEv]+0x0): multiple definition of `boost::re_detail_106800::perl_matcher<__gnu_cxx::__normal_iterator<char const*, std::string>, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::string> > >, boost::regex_traits<char, boost::c_regex_traits<char> > >::find()'  
bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\cregex.o:cregex.cpp:(.text+0x4b90): first defined here  
bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\instances.o:instances.cpp:(.text$_ZN5boost16re_detail_10680012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_14c_regex_traitsIcEEEEE5matchEv[_ZN5boost16re_detail_10680012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_14c_regex_traitsIcEEEEE5matchEv]+0x0): multiple definition of `boost::re_detail_106800::perl_matcher<char const*, std::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::c_regex_traits<char> > >::match()'  
bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\cregex.o:cregex.cpp:(.text+0x3060): first defined here  
collect2: error: ld returned 1 exit status  
    "g++"   "-Wl,--out-implib,bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\libboost_regex-gcc64-x64-1_68.dll.a" -o "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\cygboost_regex-gcc64-x64-1_68.dll" -Wl,-h -Wl,cygboost_regex-gcc64-x64-1_68.dll -shared -Wl,--start-group "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\c_regex_traits.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\cpp_regex_traits.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\cregex.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\fileiter.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\icu.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\instances.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\posix_api.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\regex.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\regex_debug.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\regex_raw_buffer.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\regex_traits_defaults.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\static_mutex.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\w32_regex_traits.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\wc_regex_traits.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\wide_posix_api.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\winstances.o" "bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\usinstances.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -m64 -std=c++03   
...failed gcc.link.dll bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\libboost_regex-gcc64-x64-1_68.dll.a bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\cygboost_regex-gcc64-x64-1_68.dll...  
...removing bin.v2\libs\regex\build\gcc-6.4.0\release\cxxstd-03-iso\target-os-cygwin\libboost_regex-gcc64-x64-1_68.dll.a  
```  
Example log: https://ci.appveyor.com/project/jeking3/iostreams/build/1.0.8-develop/job/tdn544wj0iifm7tw?fullLog=true#L2252  
  
I am trying a quick workaround (just to get iostreams building properly) by adding link=static to the command line and will update this on the result.  I didn't want to lose the issue.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-07-19 17:02:23 UTC  
> Url: https://github.com/boostorg/regex/issues/64#issuecomment-406346907  

Adding link=static to the build does work around it, but I don't understand the root cause yet.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-11 13:15:03 UTC  
> Url: https://github.com/boostorg/regex/issues/64#issuecomment-437669357  

cygwin32 failure: https://ci.appveyor.com/project/jeking3/regex/build/job/a4g7nj2cc81frfy8?fullLog=true#L1577  
  
cygwin64 failure: https://ci.appveyor.com/project/jeking3/regex/build/job/0rpxca7egwsg40jo#L1588

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-11-11 17:17:15 UTC  
> Url: https://github.com/boostorg/regex/issues/64#issuecomment-437687576  

I've pushed a fix for this - most of tests fail on cygwin though as Boost.Thread appears not to be building on cygwin:  
  
```  
gcc.compile.c++ ..\..\..\bin.v2\libs\regex\test\regex_regress_threaded.test\gcc-7.3.0cygwin\debug\cxxstd-03-iso\target-o  
s-cygwin\threadapi-win32\threading-multi\regress\basic_tests.o  
In file included from ..\..\../boost/thread/win32/once.hpp:19:0,  
                 from ..\..\../boost/thread/once.hpp:21,  
                 from regress\info.hpp:26,  
                 from regress\test_not_regex.hpp:22,  
                 from regress\test.hpp:31,  
                 from regress\basic_tests.cpp:27:  
..\..\../boost/thread/win32/thread_primitives.hpp: In function ‘bool boost::detail::win32::interlocked_bit_test_and_set(  
long int*, long int)’:  
..\..\../boost/thread/win32/thread_primitives.hpp:392:90: error: cannot convert ‘long int*’ to ‘volatile int*’ for argum  
ent ‘1’ to ‘int _InterlockedCompareExchange(volatile int*, int, int)’  
                     long const current=BOOST_INTERLOCKED_COMPARE_EXCHANGE(x,old|value,old);  
                                                                                          ^  
..\..\../boost/thread/win32/thread_primitives.hpp: In function ‘bool boost::detail::win32::interlocked_bit_test_and_rese  
t(long int*, long int)’:  
..\..\../boost/thread/win32/thread_primitives.hpp:409:91: error: cannot convert ‘long int*’ to ‘volatile int*’ for argum  
ent ‘1’ to ‘int _InterlockedCompareExchange(volatile int*, int, int)’  
                     long const current=BOOST_INTERLOCKED_COMPARE_EXCHANGE(x,old&~value,old);  
                                                                                           ^  
```  
  
Did you see this also?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-11-11 18:12:05 UTC  
> Url: https://github.com/boostorg/regex/issues/64#issuecomment-437691717  

Ah, tests pass with threadapi=pthread, but should this be required?

---

## Comment 5

> Username: jeking3  
> Created at: 2018-11-11 20:53:57 UTC  
> Url: https://github.com/boostorg/regex/issues/64#issuecomment-437703888  

I thought I filed an issue for that but I guess I did not.  I have that flag in all the CMT repository CI jobs because it was the only thing that made it work.  Looks like we need an issue opened in Boost.Thread, unless the issue is that the default threading model for cygwin in Boost.Build is set to windows instead of pthread.  Not sure which is correct.

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-11 22:29:40 UTC  
> Url: https://github.com/boostorg/regex/issues/64#issuecomment-437711243  

I looked at the Boost.Thread Appveyor jobs.  They are all failing.  Nobody seems to be addressing this, and it's partly why this issue has likely gone unnoticed.  I'll open an issue, we'll see what happens.

---

## Comment 7

> Username: Lastique  
> Created at: 2019-01-17 14:50:25 UTC  
> Url: https://github.com/boostorg/regex/issues/64#issuecomment-455198300  

FWIW, Boost.Thread develop should be fixed on Cygwin after https://github.com/boostorg/thread/pull/263. Not merged to master yet, though.  
  
I found this ticket because Boost.Log master is failing to build on Cygwin because of this issue (besides Boost.Thread). Could the fix be merged to master, please?
