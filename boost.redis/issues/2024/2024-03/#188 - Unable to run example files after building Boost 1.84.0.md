# #188 - Unable to run example files after building Boost 1.84.0. [Closed]

> Username: 9401adarsh  
> Created at: 2024-03-14 04:53:49 UTC  
> Updated at: 2024-03-14 05:14:37 UTC  
> Closed at: 2024-03-14 05:14:36 UTC  
> Url: https://github.com/boostorg/redis/issues/188  

Hello, I am currently facing errors when I try to run an example file from the Boost.redis repository. I had to download Boost 1.84.0 from source and I built them under the `home` directory, as Boost.redis is automatically present in Boost 1.84.0.   
  
Build Output:   
```  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    /home/adarsh9401/boost_1_84_0  
  
The following directory should be added to linker library paths:  
  
    /home/adarsh9401/boost_1_84_0/stage/lib  
```  
  
Now, in order to test if my source installation was valid I ran the following `boost-version.cpp` file to test if the installation was successful.   
  
```  
#include <boost/version.hpp>  
#include <iostream>  
#include <iomanip>  
  
int main()  
{  
    std::cout << "Boost version: "   
          << BOOST_VERSION / 100000  
          << "."  
          << BOOST_VERSION / 100 % 1000  
          << "."  
          << BOOST_VERSION % 100   
          << std::endl;  
    return 0;  
}  
```  
  
I compiled this file as follows:   
  
```  
g++  boost-version.cpp -o boost-version.exe -I /home/adarsh9401/boost_1_84_0  
```  
  
This file successfully compiled, and gave the following output.   
  
```  
Boost version: 1.84.0  
```  
  
Now, when I compiled this example file (https://github.com/boostorg/redis/blob/develop/example/cpp17_intro.cpp) from the Boost Redis repository using the following command - (I have named the file as boost-redis-test.cpp on my local)  
  
```  
 g++ boost-redis-test.cpp -o boost-redis.exe -I /home/adarsh9401/boost_1_84_0 -lboost_system -lboost_thread -lpthread -lssl -lcrypto  
```  
  
Have included boost_system, pthread, ssl and crypto as asio is dependent on them.   
  
I have also tried using the `-L` flag, and mapping it to `/home/adarsh9401/boost_1_84_0/stage/lib` , which returned the same error log.   
  
  
  
On Compilation:   
  
```  
  
/usr/bin/ld: /tmp/cccwGPW7.o: in function `main':  
boost-redis-test.cpp:(.text+0x377): undefined reference to `boost::redis::connection::connection(boost::asio::io_context&, boost::asio::ssl::context_base::method, unsigned long)'  
/usr/bin/ld: /tmp/cccwGPW7.o: in function `auto main::{lambda(auto:1, auto:2)#1}::operator()<boost::system::error_code, unsigned long>(boost::system::error_code, unsigned long) const':  
boost-redis-test.cpp:(.text+0x1094): undefined reference to `boost::redis::connection::cancel(boost::redis::operation)'  
/usr/bin/ld: /tmp/cccwGPW7.o: in function `boost::redis::request::check_cmd(std::basic_string_view<char, std::char_traits<char> >)':  
boost-redis-test.cpp:(.text._ZN5boost5redis7request9check_cmdESt17basic_string_viewIcSt11char_traitsIcEE[_ZN5boost5redis7request9check_cmdESt17basic_string_viewIcSt11char_traitsIcEE]+0x43): undefined reference to `boost::redis::detail::has_response(std::basic_string_view<char, std::char_traits<char> >)'  
/usr/bin/ld: /tmp/cccwGPW7.o: in function `boost::system::error_code::error_code<boost::redis::error>(boost::redis::error, boost::system::detail::enable_if<boost::system::is_error_code_enum<boost::redis::error>::value||std::is_error_code_enum<boost::redis::error>::value, void>::type*)':  
boost-redis-test.cpp:(.text._ZN5boost6system10error_codeC2INS_5redis5errorEEET_PNS0_6detail9enable_ifIXoosrNS0_18is_error_code_enumIS5_EE5valuesrSt18is_error_code_enumIS5_E5valueEvE4typeE[_ZN5boost6system10error_codeC5INS_5redis5errorEEET_PNS0_6detail9enable_ifIXoosrNS0_18is_error_code_enumIS5_EE5valuesrSt18is_error_code_enumIS5_E5valueEvE4typeE]+0x47): undefined reference to `boost::redis::make_error_code(boost::redis::error)'  
/usr/bin/ld: /tmp/cccwGPW7.o: in function `void boost::redis::request::push<char [12]>(std::basic_string_view<char, std::char_traits<char> >, char const (&) [12])':  
boost-redis-test.cpp:(.text._ZN5boost5redis7request4pushIJA12_cEEEvSt17basic_string_viewIcSt11char_traitsIcEEDpRKT_[_ZN5boost5redis7request4pushIJA12_cEEEvSt17basic_string_viewIcSt11char_traitsIcEEDpRKT_]+0x3f): undefined reference to `boost::redis::resp3::add_header(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, boost::redis::resp3::type, unsigned long)'  
/usr/bin/ld: /tmp/cccwGPW7.o: in function `auto boost::redis::connection::async_run<boost::asio::detached_t>(boost::redis::config const&, boost::redis::logger, boost::asio::detached_t)::{lambda(auto:1, boost::redis::connection*, boost::redis::config const*, boost::redis::logger)#1}::operator()<boost::asio::detail::detached_handler>(boost::asio::detail::detached_handler, boost::redis::connection*, boost::redis::config const*, boost::redis::logger) const':  
boost-redis-test.cpp:(.text._ZZN5boost5redis10connection9async_runINS_4asio10detached_tEEEDaRKNS0_6configENS0_6loggerET_ENKUlS9_PS1_PS6_S8_E_clINS3_6detail16detached_handlerEEEDaS9_SA_SB_S8_[_ZZN5boost5redis10connection9async_runINS_4asio10detached_tEEEDaRKNS0_6configENS0_6loggerET_ENKUlS9_PS1_PS6_S8_E_clINS3_6detail16detached_handlerEEEDaS9_SA_SB_S8_]+0x6d): undefined reference to `boost::redis::connection::async_run_impl(boost::redis::config const&, boost::redis::logger, boost::asio::any_completion_handler<void (boost::system::error_code)>)'  
/usr/bin/ld: /tmp/cccwGPW7.o: in function `boost::redis::resp3::add_bulk_impl<std::basic_string_view<char, std::char_traits<char> > >::add(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::basic_string_view<char, std::char_traits<char> > const&)':  
boost-redis-test.cpp:(.text._ZN5boost5redis5resp313add_bulk_implISt17basic_string_viewIcSt11char_traitsIcEEE3addERNSt7__cxx1112basic_stringIcS5_SaIcEEERKS6_[_ZN5boost5redis5resp313add_bulk_implISt17basic_string_viewIcSt11char_traitsIcEEE3addERNSt7__cxx1112basic_stringIcS5_SaIcEEERKS6_]+0x26): undefined reference to `boost::redis::resp3::boost_redis_to_bulk(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::basic_string_view<char, std::char_traits<char> >)'  
/usr/bin/ld: /tmp/cccwGPW7.o: in function `auto boost::redis::resp3::add_bulk_impl<std::tuple<char const (&) [12]> >::add(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::tuple<char const (&) [12]> const&)::{lambda((auto:1 const&)...)#1}::operator()<char [12]>(char const (&) [12]) const':  
boost-redis-test.cpp:(.text._ZZN5boost5redis5resp313add_bulk_implISt5tupleIJRA12_KcEEE3addERNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERKS7_ENKUlDpRKT_E_clIJA12_cEEEDaSL_[_ZZN5boost5redis5resp313add_bulk_implISt5tupleIJRA12_KcEEE3addERNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERKS7_ENKUlDpRKT_E_clIJA12_cEEEDaSL_]+0x39): undefined reference to `boost::redis::resp3::boost_redis_to_bulk(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::basic_string_view<char, std::char_traits<char> >)'  
collect2: error: ld returned 1 exit status  
  
```  
  
  
Could you suggest on how to proceed ?

---

## Comment 1

> Username: 9401adarsh  
> Created at: 2024-03-14 05:14:36 UTC  
> Url: https://github.com/boostorg/redis/issues/188#issuecomment-1996534699  

Apologies, I have found the fix. I forgot to add the `#include <boost/redis/src.hpp>` as mentioned in the docs. I forgot I was trying to compile a single file per se.
