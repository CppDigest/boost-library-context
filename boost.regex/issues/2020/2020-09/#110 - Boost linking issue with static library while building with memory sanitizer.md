# #110 - Boost linking issue with static library while building with memory sanitizer [Closed]

> Username: pawankumar22  
> Created at: 2020-09-10 05:56:32 UTC  
> Updated at: 2024-03-25 18:19:43 UTC  
> Closed at: 2024-03-25 18:19:43 UTC  
> Url: https://github.com/boostorg/regex/issues/110  

Hi  
  
I am getting below error while building static library uhd-types and linking with boost libraries v1.74 with memory sanitizer flag -fsanitize=memory.  
  
[ 63%] Built target uhd-types  
[ 65%] Linking CXX executable ../../bin/unit_tests  
/usr/bin/ld: ../../lib/libuhd-types.a(device_addr.cpp.o): in function boost::re_detail_107400::cpp_regex_traits_implementation::lookup_collatename(char const*, char const*) const': /root/include/boost/regex/v4/cpp_regex_traits.hpp:680: undefined reference to boost::re_detail_107400::lookup_default_collate_name(std::__1::basic_string<char, std::__1::char_traits, std::__1::allocator > const&)' /usr/bin/ld: ../../lib/libuhd-types.a(device_addr.cpp.o): in function boost::re_detail_107400::basic_regex_parser<char, boost::regex_traits<char, boost::cpp_regex_traits > >::fail(boost::regex_constants::error_type, long, std::__1::basic_string<char, std::__1::char_traits, std::__1::allocator >, long)': /root/include/boost/regex/v4/basic_regex_parser.hpp:245: undefined reference to boost::regex_error::regex_error(std::__1::basic_string<char, std::__1::char_traits, std::__1::allocator > const&, boost::regex_constants::error_type, long)' /usr/bin/ld: ../../lib/libuhd-types.a(device_addr.cpp.o): in function boost::re_detail_107400::basic_regex_creator<char, boost::regex_traits<char, boost::cpp_regex_traits > >::fixup_recursions(boost::re_detail_107400::re_syntax_base*)': /root/include/boost/regex/v4/basic_regex_creator.hpp:788: undefined reference to boost::regex_error::regex_error(std::__1::basic_string<char, std::__1::char_traits, std::__1::allocator > const&, boost::regex_constants::error_type, long)' /usr/bin/ld: /root//include/boost/regex/v4/basic_regex_creator.hpp:877: undefined reference to boost::regex_error::regex_error(std::__1::basic_string<char, std::__1::char_traits, std::__1::allocator > const&, boost::regex_constants::error_type, long)' /usr/bin/ld: ../../lib/libuhd-types.a(device_addr.cpp.o): in function boost::re_detail_107400::basic_regex_creator<char, boost::regex_traits<char, boost::cpp_regex_traits > >::create_startmaps(boost::re_detail_107400::re_syntax_base*)': /root/include/boost/regex/v4/basic_regex_creator.hpp:943: undefined reference to boost::regex_error::regex_error(std::__1::basic_string<char, std::__1::char_traits, std::__1::allocator > const&, boost::regex_constants::error_type, long)' /usr/bin/ld: ../../lib/libuhd-types.a(device_addr.cpp.o): in function boost::re_detail_107400::basic_regex_creator<char, boost::regex_traits<char, boost::cpp_regex_traits > >::create_startmap(boost::re_detail_107400::re_syntax_base*, unsigned char*, unsigned int*, unsigned char)': /root/include/boost/regex/v4/basic_regex_creator.hpp:1156: undefined reference to `boost::regex_error::regex_error(std::__1::basic_string<char, std::__1::char_traits, std::__1::allocator > const&, boost::regex_constants::error_type, long)'  
  
clang-10: error: linker command failed with exit code 1 (use -v to see invocation)  
  
I already linking uhd-types to boost libraries using target_link_libraries.  
  
Thank you  
Pawan

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-09-10 18:19:44 UTC  
> Url: https://github.com/boostorg/regex/issues/110#issuecomment-690597837  

You're going to have to post a self-contained test case along with the actual compiler command lines used, as I tried to reproduce locally and failed.  
  
My suspicion though is that you may need to build Boost.Regex with the same compiler options that you're passing to clang.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-03-25 18:19:43 UTC  
> Url: https://github.com/boostorg/regex/issues/110#issuecomment-2018630503  

Since Boost Regex is now header only, this is obsolete.
