# #1059 - Use Boost Library compilation error [Open]

> Username: weiOverlord  
> Created at: 2025-07-03 09:47:36 UTC  
> Updated at: 2025-07-03 09:47:36 UTC  
> Url: https://github.com/boostorg/boost/issues/1059  

My boost library version is 1.65. Then, when compiling the code, an exception occurred. The exception is as follows:   
  
/usr/bin/ld: ../../lib/liblepton_core.so: undefined reference to `boost::re_detail_106501::perl_matcher<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::construct_init(boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags)'  
/usr/bin/ld: ../../lib/liblepton_core.so: undefined reference to `boost::re_detail_106501::perl_matcher<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::find()'  
/usr/bin/ld: ../../lib/liblepton_core.so: undefined reference to `boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::do_assign(char const*, char const*, unsigned int)'  
collect2: error: ld returned 1 exit status  
/usr/bin/ld: ../../lib/liblepton_core.so: undefined reference to `boost::re_detail_106501::perl_matcher<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::construct_init(boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags)'  
/usr/bin/ld: ../../lib/liblepton_core.so: undefined reference to `boost::re_detail_106501::perl_matcher<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > >, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > > > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::find()'  
/usr/bin/ld: ../../lib/liblepton_core.so: undefined reference to `boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::do_assign(char const*, char const*, unsigned int)'  
collect2: error: ld returned 1 exit status  
make[2]: *** [examples/print_results/CMakeFiles/print_results.dir/build.make:127：bin/print_results] error 1  
make[2]: *** [examples/enum/CMakeFiles/enum.dir/build.make:127：bin/enum] error 1  
make[1]: *** [CMakeFiles/Makefile2:295：examples/print_results/CMakeFiles/print_results.dir/all] error 2  
make[1]: *** [CMakeFiles/Makefile2:243：examples/enum/CMakeFiles/enum.dir/all] error 2  
  
  
  
I'm not sure if it's caused by the high boost version
