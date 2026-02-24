# #151 - Assertion `jmp->type == syntax_element_jump' failed [Closed]

> Username: xayahion  
> Created at: 2021-09-09 05:05:26 UTC  
> Updated at: 2021-10-15 06:40:03 UTC  
> Closed at: 2021-10-14 10:48:09 UTC  
> Url: https://github.com/boostorg/regex/issues/151  

[root@45ea9e56a3b4 boost]# ./boost_regex_fuzzer ~/boost_regex_fuzzer-testcase  
INFO: Running with entropic power schedule (0xFF, 100).  
INFO: Seed: 3810660610  
./boost_regex_fuzzer: Running 1 inputs 1 time(s) each.  
Running: /root/fusiontest-testcase-boost_regex_fuzzer-202108160002  
boost_regex_fuzzer: ./boost/regex/v5/basic_regex_parser.hpp:3107: bool boost::re_detail_500::basic_regex_parser<char, boost::regex_traits<char>>::unwind_alts(std::ptrdiff_t) [charT = char, traits = boost::regex_traits<char>]: Assertion `jmp->type == syntax_element_jump' failed.  
==22489== ERROR: libFuzzer: deadly signal  
    #0 0x4a70a1 in __sanitizer_print_stack_trace /src/llvm-project/compiler-rt/lib/asan/asan_stack.cpp:86:3  
    #1 0x6065f8 in fuzzer::PrintStackTrace() /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerUtil.cpp:210:5  
    #2 0x5ea903 in fuzzer::Fuzzer::CrashCallback() /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:233:3  
    #3 0x7f718324a4bf  (/lib64/libpthread.so.0+0x134bf)  
    #4 0x7f718308f60a in raise (/lib64/libc.so.6+0x3960a)  
    #5 0x7f7183090930 in abort (/lib64/libc.so.6+0x3a930)  
    #6 0x7f7183087ec9  (/lib64/libc.so.6+0x31ec9)  
    #7 0x7f7183087f41 in __assert_fail (/lib64/libc.so.6+0x31f41)  
    #8 0x4ebfa1 in boost::re_detail_500::basic_regex_parser<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::unwind_alts(long) /home/boost/./boost/regex/v5/basic_regex_parser.hpp:3107:7  
    #9 0x4e7325 in boost::re_detail_500::basic_regex_parser<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::parse(char const*, char const*, unsigned int) /home/boost/./boost/regex/v5/basic_regex_parser.hpp:172:4  
    #10 0x4d4116 in boost::re_detail_500::basic_regex_implementation<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::assign(char const*, char const*, unsigned int) /home/boost/./boost/regex/v5/basic_regex.hpp:222:14  
    #11 0x4d3b9e in boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::do_assign(char const*, char const*, unsigned int) /home/boost/./boost/regex/v5/basic_regex.hpp:646:10  
    #12 0x4d39b8 in boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::assign(char const*, char const*, unsigned int) /home/boost/./boost/regex/v5/basic_regex.hpp:387:14  
    #13 0x4d3966 in boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >& boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::assign<std::__1::char_traits<char>, std::__1::allocator<char> >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned int) /home/boost/./boost/regex/v5/basic_regex.hpp:424:14  
    #14 0x4d1e25 in boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::basic_regex<std::__1::char_traits<char>, std::__1::allocator<char> >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, unsigned int) /home/boost/./boost/regex/v5/basic_regex.hpp:399:7  
    #15 0x4d1822 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:26:18  
    #16 0x5ec0a3 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #17 0x5d7812 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #18 0x5dd4de in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #19 0x606d92 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #20 0x7f718307bb26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
    #21 0x423979 in _start (/home/boost/boost_regex_fuzzer+0x423979)  
  
NOTE: libFuzzer has rudimentary signal handlers.  
      Combine libFuzzer with AddressSanitizer or similar for better crash reports.  
SUMMARY: libFuzzer: deadly signal  
  
the fuzz file and test data can get from this link  
https://github.com/zhangyiru/regex_test

---

## Comment 1

> Username: xayahion  
> Created at: 2021-09-30 03:59:36 UTC  
> Url: https://github.com/boostorg/regex/issues/151#issuecomment-930759986  

pattern string：(?xi)|?+#  
text string：*  
  
thress results of regularization algorithms  
（1）boost  
boost-1.77 not matched（**false，it should raise error because there is no valid character before "?" character**）  
  
（2）pcre2  
[root@45ea9e56a3b4 try]# ./a.out "(?xi)|?+#" "*"  
PCRE2 compilation failed at offset 6: quantifier does not follow a repeatable item  
  
（3）grep -P  
[root@45ea9e56a3b4 try]# grep -P "(?xi)|?+#" text   
grep: nothing to repeat  
  
pcre2 and grep -P both raise error  
  
through analysis, it is found that lack of judgement in syntax_question in parse_extended function.  
this PR determines whether the first character of the sub-expression is a question mark  
#157   
please review, thx~  
@jzmaddock

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-10-13 10:45:02 UTC  
> Url: https://github.com/boostorg/regex/issues/151#issuecomment-942169845  

Confirmed the original fuzzer report, expression is "\x28\x3f\x78\x69\x29\x7c\x3f\x2b\x23\xd\x2a"

---

## Comment 3

> Username: xayahion  
> Created at: 2021-10-15 06:40:03 UTC  
> Url: https://github.com/boostorg/regex/issues/151#issuecomment-944040759  

sync to v4 version  
#164
