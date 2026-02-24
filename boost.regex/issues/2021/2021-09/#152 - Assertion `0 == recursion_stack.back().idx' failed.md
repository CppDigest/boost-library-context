# #152 - Assertion `0 == recursion_stack.back().idx' failed [Closed]

> Username: xayahion  
> Created at: 2021-09-09 06:33:53 UTC  
> Updated at: 2021-10-20 16:01:54 UTC  
> Closed at: 2021-10-20 16:01:54 UTC  
> Url: https://github.com/boostorg/regex/issues/152  

[root@45ea9e56a3b4 boost]# ./boost_regex_fuzzer ~/fusiontest-testcase-boost_regex_fuzzer-202108160002   
INFO: Running with entropic power schedule (0xFF, 100).  
INFO: Seed: 3810660610  
./boost_regex_fuzzer: Running 1 inputs 1 time(s) each.  
Running: /root/fusiontest-testcase-boost_regex_fuzzer-202108160002  
boost_regex_fuzzer: ./boost/regex/v5/perl_matcher_non_recursive.hpp:1083: bool boost::re_detail_500::perl_matcher<std::__wrap_iter<const char *>, std::allocator<boost::sub_match<std::__wrap_iter<const char *>>>, boost::regex_traits<char>>::match_match() [BidiIterator = std::__wrap_iter<const char *>, Allocator = std::allocator<boost::sub_match<std::__wrap_iter<const char *>>>, traits = boost::regex_traits<char>]: Assertion `0 == recursion_stack.back().idx' failed.  
==22765== ERROR: libFuzzer: deadly signal  
    #0 0x4a7131 in __sanitizer_print_stack_trace /src/llvm-project/compiler-rt/lib/asan/asan_stack.cpp:86:3  
    #1 0x6072c8 in fuzzer::PrintStackTrace() /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerUtil.cpp:210:5  
    #2 0x5eb5d3 in fuzzer::Fuzzer::CrashCallback() /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:233:3  
    #3 0x7f49406334bf  (/lib64/libpthread.so.0+0x134bf)  
    #4 0x7f494047860a in raise (/lib64/libc.so.6+0x3960a)  
    #5 0x7f4940479930 in abort (/lib64/libc.so.6+0x3a930)  
    #6 0x7f4940470ec9  (/lib64/libc.so.6+0x31ec9)  
    #7 0x7f4940470f41 in __assert_fail (/lib64/libc.so.6+0x31f41)  
    #8 0x526a17 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_match() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:1083:7  
    #9 0x524049 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:203:14  
    #10 0x52501f in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_startmark() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:417:14  
    #11 0x524049 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:203:14  
    #12 0x51f3f7 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_prefix() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:306:4  
    #13 0x51e87c in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:196:12  
    #14 0x51c4a8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:172:11  
    #15 0x51bf70 in bool boost::regex_match<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::__wrap_iter<char const*>, std::__1::__wrap_iter<char const*>, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:39:19  
    #16 0x4d1ffd in bool boost::regex_match<std::__1::char_traits<char>, std::__1::allocator<char>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::match_results<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::const_iterator, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:67:11  
    #17 0x4d1930 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:36:18  
    #18 0x5ecd73 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #19 0x5d84e2 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #20 0x5de1ae in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #21 0x607a62 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #22 0x7f4940464b26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
    #23 0x423a09 in _start (/home/boost/boost_regex_fuzzer+0x423a09)  
  
NOTE: libFuzzer has rudimentary signal handlers.  
      Combine libFuzzer with AddressSanitizer or similar for better crash reports.  
SUMMARY: libFuzzer: deadly signal  
  
the fuzz file and test data can get from this link  
https://github.com/zhangyiru/regex_test

---

## Comment 1

> Username: xayahion  
> Created at: 2021-09-26 01:41:35 UTC  
> Url: https://github.com/boostorg/regex/issues/152#issuecomment-927211465  

@jzmaddock ping

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-09-30 09:30:09 UTC  
> Url: https://github.com/boostorg/regex/issues/152#issuecomment-931133792  

Thanks for the reports, I'll get to these as soon as I can!

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-10-13 10:47:41 UTC  
> Url: https://github.com/boostorg/regex/issues/152#issuecomment-942171847  

Confirmed.  
  
Regexp string: "\x28\x28\x1f\x28\x28\x28\x3f\x30\x29\x28\x20\x20\x29\x20\x20\x20\x29\x20\x20\x20\x20\x29\x3f\x2b\x20\x20\x20\x20\x20\x20\x20\x20\x20\x29\x20\x20\x20\x20\x29\x3f\x2b\x2e\x20\x3f\x3f\x28\x2a\x53\x4b\x49\x50\x29\x20\x3f\x20\x2a\x3f\x28\x3f\x31\x29\x2a\x3f"  
Text: "\x20\x1f\x1f\x20"
