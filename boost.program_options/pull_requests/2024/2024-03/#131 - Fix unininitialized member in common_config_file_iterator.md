# #131 Fix unininitialized member in common_config_file_iterator [Closed]

> Username: lmilunovic  
> Created at: 2024-03-03 11:54:03 UTC  
> Updated at: 2025-08-26 09:43:39 UTC  
> Closed at: 2025-08-26 09:43:39 UTC  
> Url: https://github.com/boostorg/program_options/pull/131  

Invoking default constructor of `common_config_file_iterator` leads to `allow_unregistered` being left uninitialized.  
  
Issue was discovered by running ASan build on Centos 7.  
g++ (GCC) 12.3.0  
  
  
``` /tmp/cmake-build-docker-asan/_deps/boost-src/libs/program_options/include/boost/program_options/detail/config_file.hpp:70:38: runtime error: load of value 12, which is not a valid value for type 'bool'  
pc_0x7f4142559d76###func_boost::program_options::detail::common_config_file_iterator::common_config_file_iterator(boost::program_options::detail::common_config_file_iterator const&)###file_/tmp/cmake-build-docker-asan/_deps/boost-src/libs/program_options/include/boost/program_options/detail/config_file.hpp###line_70###obj_(libboost_program_options.so.1.84.0+0x559d76)  
pc_0x7f414255a20f###func_boost::program_options::detail::basic_config_file_iterator<char>::basic_config_file_iterator(boost::program_options::detail::basic_config_file_iterator<char> const&)###file_/tmp/cmake-build-docker-asan/_deps/boost-src/libs/program_options/include/boost/program_options/detail/config_file.hpp###line_119###obj_(libboost_program_options.so.1.84.0+0x55a20f)  
pc_0x7f414255a5cb###func_std::back_insert_iterator<std::vector<boost::program_options::basic_option<char>, std::allocator<boost::program_options::basic_option<char> > > > std::copy<boost::program_options::detail::basic_config_file_iterator<char>, std::back_insert_iterator<std::vector<boost::program_options::basic_option<char>, std::allocator<boost::program_options::basic_option<char> > > > >(boost::program_options::detail::basic_config_file_iterator<char>, boost::program_options::detail::basic_config_file_iterator<char>, std::back_insert_iterator<std::vector<boost::program_options::basic_option<char>, std::allocator<boost::program_options::basic_option<char> > > >)###file_/tools/include/c++/12.2.0/bits/stl_algobase.h###line_620###obj_(libboost_program_options.so.1.84.0+0x55a5cb)  
pc_0x7f4142550bbc###func_boost::program_options::basic_parsed_options<char> boost::program_options::parse_config_file<char>(std::basic_istream<char, std::char_traits<char> >&, boost::program_options::options_description const&, bool)###file_<null>###line_0###obj_(libboost_program_options.so.1.84.0+0x550bbc)  
.  
.  
.  
  
AddressSanitizer:DEADLYSIGNAL  
 ```

---

## Comment 1

> Username: vprus  
> Created_at: 2025-08-26 08:36:28 UTC  
> Updated_at: 2025-08-26 08:36:55 UTC  
> Url: https://github.com/boostorg/program_options/pull/131#issuecomment-3223177251  

Thanks for the PR. Could you clarify on which code path does this matter.? The default constructor creates the past-the-end iterator that will only be used for comparison with a valid iterator. At no point should the uninitialized value be read or used in an if condition. Sounds like a false positive to me.

---

## Comment 2

> Username: lmilunovic  
> Created_at: 2025-08-26 08:48:00 UTC  
> Url: https://github.com/boostorg/program_options/pull/131#issuecomment-3223221452  

Huh, this was some time ago. :) I fear I am not able to answer you the question at the moment. We were enabling ASan builds in the pipeline for our codebase in order to tighten things up in general and this is one of issues that emerged at some point. I do not have the access to the codebase since I left the company since,  and I honestly do not remmember how were we using it... If I find some time I can try to reproduce it myself but that is a long shot, really...

---

## Comment 3

> Username: vprus  
> Created_at: 2025-08-26 09:43:39 UTC  
> Url: https://github.com/boostorg/program_options/pull/131#issuecomment-3223436921  

Understood, thanks!  
  
I am gonna close this issue now, as the current code is explicitly written with the assumption that the member won't be accessed at runtime.

---
