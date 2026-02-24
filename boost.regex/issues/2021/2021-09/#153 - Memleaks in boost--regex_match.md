# #153 - Memleaks in boost::regex_match [Closed]

> Username: xayahion  
> Created at: 2021-09-09 08:23:59 UTC  
> Updated at: 2023-10-24 20:48:15 UTC  
> Closed at: 2021-10-13 07:59:11 UTC  
> Url: https://github.com/boostorg/regex/issues/153  

==24376==ERROR: LeakSanitizer: detected memory leaks  
  
Direct leak of 4096 byte(s) in 1 object(s) allocated from:  
    #0 0x4cf15d in operator new(unsigned long) /src/llvm-project/compiler-rt/lib/asan/asan_new_delete.cpp:99:3  
    #1 0x51fc2f in boost::re_detail_500::mem_block_cache::get() /home/boost/./boost/regex/v5/mem_block_cache.hpp:60:13  
    #2 0x51f9ac in boost::re_detail_500::get_mem_block() /home/boost/./boost/regex/v5/mem_block_cache.hpp:166:39  
    #3 0x52cb82 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::extend_stack() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:230:46  
    #4 0x52cea8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::push_recursion(int, boost::re_detail_500::re_syntax_base const*, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >*, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >*) /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:373:7  
    #5 0x5264c9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_endmark() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:1065:13  
    #6 0x524bb9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:205:14  
    #7 0x51f417 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_prefix() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:307:4  
    #8 0x51e87c in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:196:12  
    #9 0x51c4a8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:172:11  
    #10 0x51bf70 in bool boost::regex_match<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::__wrap_iter<char const*>, std::__1::__wrap_iter<char const*>, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:39:19  
    #11 0x4d1ffd in bool boost::regex_match<std::__1::char_traits<char>, std::__1::allocator<char>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::match_results<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::const_iterator, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:67:11  
    #12 0x4d1930 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:36:18  
    #13 0x5ecea3 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #14 0x5d8612 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #15 0x5de2de in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #16 0x607b92 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #17 0x7f4641fbab26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
  
Indirect leak of 1036288 byte(s) in 253 object(s) allocated from:  
    #0 0x4cf15d in operator new(unsigned long) /src/llvm-project/compiler-rt/lib/asan/asan_new_delete.cpp:99:3  
    #1 0x51fc2f in boost::re_detail_500::mem_block_cache::get() /home/boost/./boost/regex/v5/mem_block_cache.hpp:60:13  
    #2 0x51f9ac in boost::re_detail_500::get_mem_block() /home/boost/./boost/regex/v5/mem_block_cache.hpp:166:39  
    #3 0x52cb82 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::extend_stack() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:230:46  
    #4 0x52cfa9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::push_repeater_count(int, boost::re_detail_500::repeater_count<std::__1::__wrap_iter<char const*> >**) /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:343:7  
    #5 0x529207 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_rep() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:623:7  
    #6 0x524bb9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:205:14  
    #7 0x51f417 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_prefix() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:307:4  
    #8 0x51e87c in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:196:12  
    #9 0x51c4a8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:172:11  
    #10 0x51bf70 in bool boost::regex_match<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::__wrap_iter<char const*>, std::__1::__wrap_iter<char const*>, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:39:19  
    #11 0x4d1ffd in bool boost::regex_match<std::__1::char_traits<char>, std::__1::allocator<char>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::match_results<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::const_iterator, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:67:11  
    #12 0x4d1930 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:36:18  
    #13 0x5ecea3 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #14 0x5d8612 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #15 0x5de2de in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #16 0x607b92 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #17 0x7f4641fbab26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
  
Indirect leak of 1032192 byte(s) in 252 object(s) allocated from:  
    #0 0x4cf15d in operator new(unsigned long) /src/llvm-project/compiler-rt/lib/asan/asan_new_delete.cpp:99:3  
    #1 0x51fc2f in boost::re_detail_500::mem_block_cache::get() /home/boost/./boost/regex/v5/mem_block_cache.hpp:60:13  
    #2 0x51f9ac in boost::re_detail_500::get_mem_block() /home/boost/./boost/regex/v5/mem_block_cache.hpp:166:39  
    #3 0x52cb82 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::extend_stack() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:230:46  
    #4 0x52cea8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::push_recursion(int, boost::re_detail_500::re_syntax_base const*, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >*, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >*) /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:373:7  
    #5 0x5264c9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_endmark() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:1065:13  
    #6 0x524bb9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:205:14  
    #7 0x51f417 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_prefix() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:307:4  
    #8 0x51e87c in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:196:12  
    #9 0x51c4a8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:172:11  
    #10 0x51bf70 in bool boost::regex_match<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::__wrap_iter<char const*>, std::__1::__wrap_iter<char const*>, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:39:19  
    #11 0x4d1ffd in bool boost::regex_match<std::__1::char_traits<char>, std::__1::allocator<char>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::match_results<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::const_iterator, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:67:11  
    #12 0x4d1930 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:36:18  
    #13 0x5ecea3 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #14 0x5d8612 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #15 0x5de2de in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #16 0x607b92 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #17 0x7f4641fbab26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
  
Indirect leak of 16384 byte(s) in 4 object(s) allocated from:  
    #0 0x4cf15d in operator new(unsigned long) /src/llvm-project/compiler-rt/lib/asan/asan_new_delete.cpp:99:3  
    #1 0x51fc2f in boost::re_detail_500::mem_block_cache::get() /home/boost/./boost/regex/v5/mem_block_cache.hpp:60:13  
    #2 0x51f9ac in boost::re_detail_500::get_mem_block() /home/boost/./boost/regex/v5/mem_block_cache.hpp:166:39  
    #3 0x52cb82 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::extend_stack() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:230:46  
    #4 0x52c987 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::push_matched_paren(int, boost::sub_match<std::__1::__wrap_iter<char const*> > const&) /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:252:7  
    #5 0x525e0b in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_startmark() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:545:10  
    #6 0x524bb9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:205:14  
    #7 0x51f417 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_prefix() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:307:4  
    #8 0x51e87c in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:196:12  
    #9 0x51c4a8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:172:11  
    #10 0x51bf70 in bool boost::regex_match<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::__wrap_iter<char const*>, std::__1::__wrap_iter<char const*>, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:39:19  
    #11 0x4d1ffd in bool boost::regex_match<std::__1::char_traits<char>, std::__1::allocator<char>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::match_results<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::const_iterator, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:67:11  
    #12 0x4d1930 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:36:18  
    #13 0x5ecea3 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #14 0x5d8612 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #15 0x5de2de in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #16 0x607b92 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #17 0x7f4641fbab26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
  
Indirect leak of 8192 byte(s) in 2 object(s) allocated from:  
    #0 0x4cf15d in operator new(unsigned long) /src/llvm-project/compiler-rt/lib/asan/asan_new_delete.cpp:99:3  
    #1 0x51fc2f in boost::re_detail_500::mem_block_cache::get() /home/boost/./boost/regex/v5/mem_block_cache.hpp:60:13  
    #2 0x51f9ac in boost::re_detail_500::get_mem_block() /home/boost/./boost/regex/v5/mem_block_cache.hpp:166:39  
    #3 0x52cb82 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::extend_stack() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:230:46  
    #4 0x530491 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::push_single_repeat(unsigned long, boost::re_detail_500::re_repeat const*, std::__1::__wrap_iter<char const*>, int) /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:358:7  
    #5 0x52a123 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_char_repeat() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:845:10  
    #6 0x524bb9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:205:14  
    #7 0x51f417 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_prefix() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:307:4  
    #8 0x51e87c in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:196:12  
    #9 0x51c4a8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:172:11  
    #10 0x51bf70 in bool boost::regex_match<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::__wrap_iter<char const*>, std::__1::__wrap_iter<char const*>, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:39:19  
    #11 0x4d1ffd in bool boost::regex_match<std::__1::char_traits<char>, std::__1::allocator<char>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::match_results<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::const_iterator, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:67:11  
    #12 0x4d1930 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:36:18  
    #13 0x5ecea3 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #14 0x5d8612 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #15 0x5de2de in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #16 0x607b92 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #17 0x7f4641fbab26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
  
Indirect leak of 4096 byte(s) in 1 object(s) allocated from:  
    #0 0x4cf15d in operator new(unsigned long) /src/llvm-project/compiler-rt/lib/asan/asan_new_delete.cpp:99:3  
    #1 0x51fc2f in boost::re_detail_500::mem_block_cache::get() /home/boost/./boost/regex/v5/mem_block_cache.hpp:60:13  
    #2 0x51f9ac in boost::re_detail_500::get_mem_block() /home/boost/./boost/regex/v5/mem_block_cache.hpp:166:39  
    #3 0x52cb82 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::extend_stack() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:230:46  
    #4 0x530b1d in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::push_recursion_pop() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:1819:7  
    #5 0x52bd3d in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_recursion() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:1026:4  
    #6 0x524bb9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:205:14  
    #7 0x51f417 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_prefix() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:307:4  
    #8 0x51e87c in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:196:12  
    #9 0x51c4a8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:172:11  
    #10 0x51bf70 in bool boost::regex_match<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::__wrap_iter<char const*>, std::__1::__wrap_iter<char const*>, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:39:19  
    #11 0x4d1ffd in bool boost::regex_match<std::__1::char_traits<char>, std::__1::allocator<char>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::match_results<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::const_iterator, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:67:11  
    #12 0x4d1930 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:36:18  
    #13 0x5ecea3 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #14 0x5d8612 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #15 0x5de2de in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #16 0x607b92 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #17 0x7f4641fbab26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
  
Indirect leak of 4096 byte(s) in 1 object(s) allocated from:  
    #0 0x4cf15d in operator new(unsigned long) /src/llvm-project/compiler-rt/lib/asan/asan_new_delete.cpp:99:3  
    #1 0x51fc2f in boost::re_detail_500::mem_block_cache::get() /home/boost/./boost/regex/v5/mem_block_cache.hpp:60:13  
    #2 0x51f9ac in boost::re_detail_500::get_mem_block() /home/boost/./boost/regex/v5/mem_block_cache.hpp:166:39  
    #3 0x52cb82 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::extend_stack() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:230:46  
    #4 0x52cfa9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::push_repeater_count(int, boost::re_detail_500::repeater_count<std::__1::__wrap_iter<char const*> >**) /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:343:7  
    #5 0x52bf58 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_recursion() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:1042:7  
    #6 0x524bb9 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:205:14  
    #7 0x51f417 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_prefix() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:307:4  
    #8 0x51e87c in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:196:12  
    #9 0x51c4a8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:172:11  
    #10 0x51bf70 in bool boost::regex_match<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::__wrap_iter<char const*>, std::__1::__wrap_iter<char const*>, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:39:19  
    #11 0x4d1ffd in bool boost::regex_match<std::__1::char_traits<char>, std::__1::allocator<char>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::match_results<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::const_iterator, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:67:11  
    #12 0x4d1930 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:36:18  
    #13 0x5ecea3 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #14 0x5d8612 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #15 0x5de2de in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #16 0x607b92 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #17 0x7f4641fbab26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
  
Indirect leak of 4096 byte(s) in 1 object(s) allocated from:  
    #0 0x4cf15d in operator new(unsigned long) /src/llvm-project/compiler-rt/lib/asan/asan_new_delete.cpp:99:3  
    #1 0x51fc2f in boost::re_detail_500::mem_block_cache::get() /home/boost/./boost/regex/v5/mem_block_cache.hpp:60:13  
    #2 0x51f9ac in boost::re_detail_500::get_mem_block() /home/boost/./boost/regex/v5/mem_block_cache.hpp:166:39  
    #3 0x51eb0c in boost::re_detail_500::save_state_init::save_state_init(boost::re_detail_500::saved_state**, boost::re_detail_500::saved_state**) /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:102:41  
    #4 0x51e5d5 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:179:20  
    #5 0x51c4a8 in boost::re_detail_500::perl_matcher<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match() /home/boost/./boost/regex/v5/perl_matcher_common.hpp:172:11  
    #6 0x51bf70 in bool boost::regex_match<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::__wrap_iter<char const*>, std::__1::__wrap_iter<char const*>, boost::match_results<std::__1::__wrap_iter<char const*>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:39:19  
    #7 0x4d1ffd in bool boost::regex_match<std::__1::char_traits<char>, std::__1::allocator<char>, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> > const&, boost::match_results<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >::const_iterator, std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags) /home/boost/./boost/regex/v5/regex_match.hpp:67:11  
    #8 0x4d1930 in LLVMFuzzerTestOneInput /home/boost/../boost_regex_fuzzer.cc:36:18  
    #9 0x5ecea3 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerLoop.cpp:599:15  
    #10 0x5d8612 in fuzzer::RunOneTest(fuzzer::Fuzzer*, char const*, unsigned long) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:323:6  
    #11 0x5de2de in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerDriver.cpp:856:9  
    #12 0x607b92 in main /src/llvm-project/compiler-rt/lib/fuzzer/FuzzerMain.cpp:20:10  
    #13 0x7f4641fbab26 in __libc_start_main (/lib64/libc.so.6+0x25b26)  
  
SUMMARY: AddressSanitizer: 2109440 byte(s) leaked in 515 allocation(s).  
  
INFO: a leak has been found in the initial corpus.  
  
INFO: to ignore leaks on libFuzzer side use -detect_leaks=0.  
  
the fuzz file and test data can get from this link  
https://github.com/zhangyiru/regex_test

---

## Comment 1

> Username: xayahion  
> Created at: 2021-09-18 06:37:18 UTC  
> Updated at: 2021-09-18 06:55:45 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-922222357  

#155   
this commit can solve memory leak.  
the analysis is as follows：  
first, memory leak position is `std::__1::allocator<boost::sub_match<std::__1::__wrap_iter<char const*> > >, boost::regex_traits<char, boost::cpp_regex_traits > >::extend_stack() /home/boost/./boost/regex/v5/perl_matcher_non_recursive.hpp:230:46`  
  
![image-20210918113213719](https://user-images.githubusercontent.com/15630061/133879283-87476527-14e2-47c3-85cb-71768e0fd91f.png)  
  
when the used_block_count decrease to zero, it will result in an abnormal process, it will not free memory so memory leak happens.  
  
put_mem_block and get_mem_block is paired.   
we can see that used_block_count is decreasing when get_mem_block is called, but there is no increase of used_block_count in unwind_ extra_block function, so the capability in the cache pool will be zero, resulting in an abnormal process( raise_error ). The memory has not been released, resulting in leakage  
  
![image-20210918113800234](https://user-images.githubusercontent.com/15630061/133879480-3b2bed5e-a407-4172-8d36-4b2c4cfc653a.png)  
  
please review this commit, thanks

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-10-12 11:33:19 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-940924491  

Thanks for the info, I confess I *really* hate fuzzer reports because they generate state machine which are incomprehensible by mere humans :(  
  
The fix is correct, but not sufficient: used_block_count is there to limit how large the engine's memory usage grows, even with your fix the throw statement can still be reached as a result of too much memory being used.  
  
  
For my further reference:  
  
The expression triggering the issue is: \x28\x28\x1f\x28\x28\x28\x3f\x31\x29\x8\x29\xf3\x29\x21\x3d\x29\x3f\x3f\xe4\x2e\x2b\x1f\x3f\x7c\x28\x3f\x21\x28\x28\x61\x3f\x3f\x28\x2a\x53\x4b\x49\x50\x29\x4\x3f\x2e\x2a\x3f\x28\x3f\x31\x29\x30\x77\x29\x29\x29\x49\x29\x61\x63\xbe\x45\x30\xa1\x5c\xfe\x5\xd2\x26\xc0\xf5\x17\x6c\xd4\xc3\x72\xe9\xb6\x74  
  
Text: \xb9\x32\x7c\xbc\x2d\xa0\xb\x85\xf3\xcf\x93\xa7\xd0\x44\x7b\x21\x12\x93\x6a\x7b\x72\x6d\x1e\x69\x56\x31\x37\x30\x31\x34\x31\x31\x38\x33\x34\x36\x30\x34\x36\x39\x32\x33\x31\x37\x33\x31\x36\x38\x37\x33\x30\x33\x37\x31\x35\x38\x38\x34\x31\x30\x35\x37\x32\x37\xba\x3e\x4\xc7\x27\xe9\xae\xf2\x0\x84\x47\x1f\xdc\xc9\x4c\xe5\xbc\xcf\x17\x31\x37\x30\x31\x34\x31\x31\x38\x33\x34\x36\x30\x34\x36\x39\x32\x33\x31\x37\x33\x31\x36\x38\x37\x33\x30\x33\x37\x31\x35\x38\x38\x34\x31\x2c\xd6\xf5\x42\xe4\x13\x15\xde\x7e\xa1\x84\x5a\x32\xf5\x67\xd5\x13\x9a\xd1\xa6\x99\x18\x23\xf7\x5c\xf6\x40\x80\x9c\x79\xbe\x4a\xc2\x54\x94\x93\xa3\x50\x27\xaf\xd4\xc4\x3b\xd3\x49\x95\xe7\xa9\xa0\xa5\x14\x81\xd2\x9a\x77\x92\xa8\x81\xb0\xf4\x5b\xa8\x9c\x3e\x17\x3b\xbd\x86\x26\x9a\x57\x56\x12\xce\x8c\x4a\xca\x68\x86\x3d\xf5\xba\x75\xab\xb1\x76\x2d\xd\xf1\xc\x24\x5e\xc5\x6d\xc8\xdf\xa6\x18\x86\x5e\x56

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-10-12 18:26:31 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-941269213  

You're fix was correct after all: the issue was that since the stack-space-exhausted flag has not been reset it throw's twice, and the second throw escapes the stack unwinding mechanisms.  
  
Merged tests and your fix to https://github.com/boostorg/regex/pull/162

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-10-13 07:59:11 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-942031970  

Fixes merged.

---

## Comment 5

> Username: xayahion  
> Created at: 2021-10-15 06:34:28 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-944038528  

sync to v4 version  
#164

---

## Comment 6

> Username: jyu2-git  
> Created at: 2023-10-16 15:36:48 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-1764758265  

It seems both MS and clang failed with this test with Segmentation fault.  Could some one tell me if this is compiler problem?  
Thanks.  
Jennifer  
  
Unhandled exception at 0x00007FFE342E7799 (ntdll.dll) in issue153.exe: 0xC00000FD: Stack overflow (parameters: 0x0000000000000001, 0x000000DB07CD3FE8).  
     ntdll.dll!00007ffe342e7799()    Unknown  
     KernelBase.dll!00007ffe31c11e5b()    Unknown  
     ucrtbased.dll!00007ffd9ed3e852()    Unknown  
     ucrtbased.dll!00007ffd9ed3ca91()    Unknown  
     ucrtbased.dll!00007ffd9ed40275()    Unknown  
>    issue153.exe!operator delete(void * block) Line 38    C++  
     issue153.exe!operator delete(void * block, unsigned __int64 __formal) Line 32    C++  
     issue153.exe!std::_Deallocate<16,0>(void * _Ptr, unsigned __int64 _Bytes) Line 259    C++  
     issue153.exe!std::allocator<boost::sub_match<const char *>>::deallocate(boost::sub_match<const char *> * const _Ptr, const unsigned __int64 _Count) Line 833    C++  
     issue153.exe!std::vector<boost::sub_match<const char *>,std::allocator<boost::sub_match<const char *>>>::_Tidy() Line 1909    C++  
     issue153.exe!std::vector<boost::sub_match<const char *>,std::allocator<boost::sub_match<const char *>>>::~vector() Line 807    C++  
     issue153.exe!boost::match_results<const char *,std::allocator<boost::sub_match<const char *>>>::~match_results() Line 90    C++  
     issue153.exe!boost::re_detail_500::recursion_info<boost::match_results<const char *,std::allocator<boost::sub_match<const char *>>>>::~recursion_info() Line 331    C++  
     [External Code]      
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind_recursion_pop(bool r) Line 1801    C++  
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind(bool have_match) Line 1260    C++  
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind_commit(bool b) Line 1825    C++  
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind(bool have_match) Line 1260    C++  
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind_commit(bool b) Line 1825    C++  
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind(bool have_match) Line 1260    C++  
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind_commit(bool b) Line 1825    C++  
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind(bool have_match) Line 1260    C++  
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind_commit(bool b) Line 1825    C++  
     issue153.exe!boost::re_detail_500::perl_matcher<const char *,std::allocator<boost::sub_match<const char *>>,boost::regex_traits<char,boost::w32_regex_traits<char>>>::unwind(bool have_match) Line 1260    C++  
<....>  
     The maximum number of stack frames supported by Visual Studio has been exceeded.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2023-10-16 16:39:31 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-1764867984  

We try our best to avoid infinite recursions like this, but I notice that the expression must be using recursive sub-expressions, and these are by their very nature "recursive".  I would need both the actual regular expression and the text it was matching to be able to investigate further.

---

## Comment 8

> Username: jyu2-git  
> Created at: 2023-10-24 03:56:21 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-1776500393  

Hi, anyone has idea?  
  
Hi @jzmaddock,   
The expression triggering the issue is: \x28\x28\x1f\x28\x28\x28\x3f\x31\x29\x8\x29\xf3\x29\x21\x3d\x29\x3f\x3f\xe4\x2e\x2b\x1f\x3f\x7c\x28\x3f\x21\x28\x28\x61\x3f\x3f\x28\x2a\x53\x4b\x49\x50\x29\x4\x3f\x2e\x2a\x3f\x28\x3f\x31\x29\x30\x77\x29\x29\x29\x49\x29\x61\x63\xbe\x45\x30\xa1\x5c\xfe\x5\xd2\x26\xc0\xf5\x17\x6c\xd4\xc3\x72\xe9\xb6\x74  
  
Text: \xb9\x32\x7c\xbc\x2d\xa0\xb\x85\xf3\xcf\x93\xa7\xd0\x44\x7b\x21\x12\x93\x6a\x7b\x72\x6d\x1e\x69\x56\x31\x37\x30\x31\x34\x31\x31\x38\x33\x34\x36\x30\x34\x36\x39\x32\x33\x31\x37\x33\x31\x36\x38\x37\x33\x30\x33\x37\x31\x35\x38\x38\x34\x31\x30\x35\x37\x32\x37\xba\x3e\x4\xc7\x27\xe9\xae\xf2\x0\x84\x47\x1f\xdc\xc9\x4c\xe5\xbc\xcf\x17\x31\x37\x30\x31\x34\x31\x31\x38\x33\x34\x36\x30\x34\x36\x39\x32\x33\x31\x37\x33\x31\x36\x38\x37\x33\x30\x33\x37\x31\x35\x38\x38\x34\x31\x2c\xd6\xf5\x42\xe4\x13\x15\xde\x7e\xa1\x84\x5a\x32\xf5\x67\xd5\x13\x9a\xd1\xa6\x99\x18\x23\xf7\x5c\xf6\x40\x80\x9c\x79\xbe\x4a\xc2\x54\x94\x93\xa3\x50\x27\xaf\xd4\xc4\x3b\xd3\x49\x95\xe7\xa9\xa0\xa5\x14\x81\xd2\x9a\x77\x92\xa8\x81\xb0\xf4\x5b\xa8\x9c\x3e\x17\x3b\xbd\x86\x26\x9a\x57\x56\x12\xce\x8c\x4a\xca\x68\x86\x3d\xf5\xba\x75\xab\xb1\x76\x2d\xd\xf1\xc\x24\x5e\xc5\x6d\xc8\xdf\xa6\x18\x86\x5e\x56  
  
Is this what you ask for?

---

## Comment 9

> Username: jzmaddock  
> Created at: 2023-10-24 15:27:38 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-1777485836  

Apologies for the delay, I tried:  
  
```  
int main(int argc, char** argv)  
{  
   boost::regex e("\x28\x28\x1f\x28\x28\x28\x3f\x31\x29\x8\x29\xf3\x29\x21\x3d\x29\x3f\x3f\xe4\x2e\x2b\x1f\x3f\x7c\x28\x3f\x21\x28\x28\x61\x3f\x3f\x28\x2a\x53\x4b\x49\x50\x29\x4\x3f\x2e\x2a\x3f\x28\x3f\x31\x29\x30\x77\x29\x29\x29\x49\x29\x61\x63\xbe\x45\x30\xa1\x5c\xfe\x5\xd2\x26\xc0\xf5\x17\x6c\xd4\xc3\x72\xe9\xb6\x74");  
   std::string s("\xb9\x32\x7c\xbc\x2d\xa0\xb\x85\xf3\xcf\x93\xa7\xd0\x44\x7b\x21\x12\x93\x6a\x7b\x72\x6d\x1e\x69\x56\x31\x37\x30\x31\x34\x31\x31\x38\x33\x34\x36\x30\x34\x36\x39\x32\x33\x31\x37\x33\x31\x36\x38\x37\x33\x30\x33\x37\x31\x35\x38\x38\x34\x31\x30\x35\x37\x32\x37\xba\x3e\x4\xc7\x27\xe9\xae\xf2\x0\x84\x47\x1f\xdc\xc9\x4c\xe5\xbc\xcf\x17\x31\x37\x30\x31\x34\x31\x31\x38\x33\x34\x36\x30\x34\x36\x39\x32\x33\x31\x37\x33\x31\x36\x38\x37\x33\x30\x33\x37\x31\x35\x38\x38\x34\x31\x2c\xd6\xf5\x42\xe4\x13\x15\xde\x7e\xa1\x84\x5a\x32\xf5\x67\xd5\x13\x9a\xd1\xa6\x99\x18\x23\xf7\x5c\xf6\x40\x80\x9c\x79\xbe\x4a\xc2\x54\x94\x93\xa3\x50\x27\xaf\xd4\xc4\x3b\xd3\x49\x95\xe7\xa9\xa0\xa5\x14\x81\xd2\x9a\x77\x92\xa8\x81\xb0\xf4\x5b\xa8\x9c\x3e\x17\x3b\xbd\x86\x26\x9a\x57\x56\x12\xce\x8c\x4a\xca\x68\x86\x3d\xf5\xba\x75\xab\xb1\x76\x2d\xd\xf1\xc\x24\x5e\xc5\x6d\xc8\xdf\xa6\x18\x86\x5e\x56");  
   boost::smatch what;  
   bool r = regex_search(s, what, e);  
   if (r)  
   {  
      std::cout << "Matched:\n";  
      std::cout << what[0] << std::endl;  
   }  
   else  
   {  
      std::cout << "No Match!" << std::endl;  
   }  
}  
```  
  
But it doesn't reproduce the issue for me with either msvc or GCC and Boost-1.83.

---

## Comment 10

> Username: jyu2-git  
> Created at: 2023-10-24 15:41:02 UTC  
> Updated at: 2023-10-24 15:44:01 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-1777510281  

Hi @jzmaddock,   
Thank you so much for trying.  
Which msvc you are using?  I am using VS 19.33.31629.    
  
[2 test]$ cl /EHs /GR /Z7 /Od /Ob0 /W4 /MDd -D_CRT_USE_BUILTIN_OFFSETOF -DBO  
OST_ALL_NO_LIB=1 "-I..\..\.." issue153.cpp  
Microsoft (R) C/C++ Optimizing Compiler Version 19.33.31629 for x64  
Copyright (C) Microsoft Corporation. All rights reserved.  
  
issue153.cpp  
issue153.cpp: warning C4117: macro name '_CRT_USE_BUILTIN_OFFSETOF' is reserved, '#define' ignored  
issue153.cpp(16): warning C4100: 'argv': unreferenced formal parameter  
issue153.cpp(16): warning C4100: 'argc': unreferenced formal parameter  
Microsoft (R) Incremental Linker Version 14.33.31629.0  
Copyright (C) Microsoft Corporation. All rights reserved.  
  
/out:issue153.exe  
/debug  
issue153.obj  
[0 test]$ ./issue153.exe  
Segmentation fault  
  
  
the issue153.cpp:  
int main(int argc, char** argv)  
{  
   try  
   {  
      boost::regex e("\x28\x28\x1f\x28\x28\x28\x3f\x31\x29\x8\x29\xf3\x29\x21\x3d\x29\x3f\x3f\xe4\x2e\x2b\x1f\x3f\x7c\x28\x3f\x21\x28\x28\x61\x3f\x3f\x28\x2a\x53\x4b\x49\x50\x29\x4\x3f\x2e\x2a\x3f\x28\x3f\x31\x29\x30\x77\x29\x29\x29\x49\x29\x61\x63\xbe\x45\x30\xa1\x5c\xfe\x5\xd2\x26\xc0\xf5\x17\x6c\xd4\xc3\x72\xe9\xb6\x74");  
      if (boost::regex_match("\xb9\x32\x7c\xbc\x2d\xa0\xb\x85\xf3\xcf\x93\xa7\xd0\x44\x7b\x21\x12\x93\x6a\x7b\x72\x6d\x1e\x69\x56\x31\x37\x30\x31\x34\x31\x31\x38\x33\x34\x36\x30\x34\x36\x39\x32\x33\x31\x37\x33\x31\x36\x38\x37\x33\x30\x33\x37\x31\x35\x38\x38\x34\x31\x30\x35\x37\x32\x37\xba\x3e\x4\xc7\x27\xe9\xae\xf2\x01\x84\x47\x1f\xdc\xc9\x4c\xe5\xbc\xcf\x17\x31\x37\x30\x31\x34\x31\x31\x38\x33\x34\x36\x30\x34\x36\x39\x32\x33\x31\x37\x33\x31\x36\x38\x37\x33\x30\x33\x37\x31\x35\x38\x38\x34\x31\x2c\xd6\xf5\x42\xe4\x13\x15\xde\x7e\xa1\x84\x5a\x32\xf5\x67\xd5\x13\x9a\xd1\xa6\x99\x18\x23\xf7\x5c\xf6\x40\x80\x9c\x79\xbe\x4a\xc2\x54\x94\x93\xa3\x50\x27\xaf\xd4\xc4\x3b\xd3\x49\x95\xe7\xa9\xa0\xa5\x14\x81\xd2\x9a\x77\x92\xa8\x81\xb0\xf4\x5b\xa8\x9c\x3e\x17\x3b\xbd\x86\x26\x9a\x57\x56\x12\xce\x8c\x4a\xca\x68\x86\x3d\xf5\xba\x75\xab\xb1\x76\x2d\xd\xf1\xc\x24\x5e\xc5\x6d\xc8\xdf\xa6\x18\x86\x5e\x56", e, boost::regex_constants::match_default | boost::regex_constants::match_partial))  
      {  
         std::cout << "OK" << std::endl;  
      }  
   }  
   catch (const boost::regex_error& e)  
   {  
      (void)( (!!(e.code() == boost::regex_constants::error_complexity)) || (_wassert(L"e.code() == boost::regex_constants::error_complexity", L"issue153.cpp", (unsigned)(28)), 0) );  
      std::cout << e.what() << std::endl;  
   }  
   return 0;  
}

---

## Comment 11

> Username: jyu2-git  
> Created at: 2023-10-24 20:48:15 UTC  
> Url: https://github.com/boostorg/regex/issues/153#issuecomment-1778013703  

It looks like a compiler bug here.  If const string is passed directly to regx_match, test fail.  But if assign string to variable, and passing variable to the call, test passes.  
Thanks @jzmaddock for help.
