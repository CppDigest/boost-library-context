# #109 - Undefined referecnce issue [Closed]

> Username: kjaisinghyadav  
> Created at: 2020-06-24 11:50:07 UTC  
> Updated at: 2024-03-25 18:21:23 UTC  
> Closed at: 2024-03-25 18:21:23 UTC  
> Url: https://github.com/boostorg/regex/issues/109  

i compiled  source code in boost 57 version and i didn't face any issue but when i compiled in boost 67 version i am getting Undefined reference as below  
xxxx.o: In function `bool boost::regex_match<char, std::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(char const*, boost::match_results<char const*, std::allocator<boost::sub_match<char const*> > >&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags)':  
xxxx.cpp:(.text._ZN5boost11regex_matchIcSaINS_9sub_matchIPKcEEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEEEbPKT_RNS_13match_resultsISC_T0_EERKNS_11basic_regexISA_T1_EENS_15regex_constants12_match_flagsE[_ZN5boost11regex_matchIcSaINS_9sub_matchIPKcEEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEEEbPKT_RNS_13match_resultsISC_T0_EERKNS_11basic_regexISA_T1_EENS_15regex_constants12_match_flagsE]+0xbf): undefined reference to `boost::re_detail_106700::perl_matcher<char const*, std::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::construct_init(boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags)'  
xxxx.o: In function `boost::re_detail_106700::perl_matcher<char const*, std::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_imp()':  
xxxx.cpp:(.text._ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE9match_impEv[_ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE9match_impEv]+0x1f): undefined reference to `boost::re_detail_106700::get_mem_block()'  
xxxx.cpp:(.text._ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE9match_impEv[_ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE9match_impEv]+0x4d5): undefined reference to `boost::re_detail_106700::verify_options(unsigned int, boost::regex_constants::_match_flags)'  
http_client.o: In function `boost::re_detail_106700::perl_matcher<char const*, std::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_startmark()':  
xxxx.cpp:(.text._ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE15match_startmarkEv[_ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE15match_startmarkEv]+0x4de): undefined reference to `boost::re_detail_106700::raise_runtime_error(std::runtime_error const&)'  
http_client.o: In function `boost::re_detail_106700::perl_matcher<char const*, std::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_all_states()':  
xxxx.cpp:(.text._ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE16match_all_statesEv[_ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE16match_all_statesEv]+0x3b1): undefined reference to `boost::re_detail_106700::get_default_error_string(boost::regex_constants::error_type)'  
http_client.o: In function `boost::re_detail_106700::perl_matcher<char const*, std::allocator<boost::sub_match<char const*> >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::match_long_set()':  
xxxx.cpp:(.text._ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE14match_long_setEv[_ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE14match_long_setEv]+0x15c): undefined reference to `boost::re_detail_106700::cpp_regex_traits_implementation<char>::transform(char const*, char const*) const'  
xxxx.cpp:(.text._ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE14match_long_setEv[_ZN5boost16re_detail_10670012perl_matcherIPKcSaINS_9sub_matchIS3_EEENS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEE14match_long_setEv]+0x2ac): undefined reference to `boost::re_detail_106700::cpp_regex_traits_implementation<char>::transform_primary(char const*, char const*) const'  
yyyy.o: In function `bool boost::regex_match<std::char_traits<char>, std::allocator<char>, char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >(std::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags)':  
yyyy.cpp:(.text._ZN5boost11regex_matchISt11char_traitsIcESaIcEcNS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEEEbRKSbIT1_T_T0_ERKNS_11basic_regexIS8_T2_EENS_15regex_constants12_match_flagsE[_ZN5boost11regex_matchISt11char_traitsIcESaIcEcNS_12regex_traitsIcNS_16cpp_regex_traitsIcEEEEEEbRKSbIT1_T_T0_ERKNS_11basic_regexIS8_T2_EENS_15regex_constants12_match_flagsE]+0xea): undefined reference to `boost::re_detail_106700::perl_matcher<__gnu_cxx::__normal_iterator<char const*, std::string>, std::allocator<boost::sub_match<__gnu_cxx::__normal_iterator<char const*, std::string> > >, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::construct_init(boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > > const&, boost::regex_constants::_match_flags)'  
  
Compiler: icc  
In make file i am using -lboost_regex flag also

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-03-25 18:21:23 UTC  
> Url: https://github.com/boostorg/regex/issues/109#issuecomment-2018633166  

Obsolete, since regex is now header only.
