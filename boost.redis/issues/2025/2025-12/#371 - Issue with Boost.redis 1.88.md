# #371 - Issue with Boost.redis 1.88 [Closed]

> Username: dayashankerprasad  
> Created at: 2025-12-12 04:38:26 UTC  
> Updated at: 2025-12-12 18:52:28 UTC  
> Closed at: 2025-12-12 18:52:28 UTC  
> Url: https://github.com/boostorg/redis/issues/371  

I wrote a test application to connect with redis. I am getting linking error when I compile the code as below  
`g++ -std=c++20 boostRedis.cpp -pthread -lboost_system -lboost_thread -lssl -lcrypto`  
  
Error:  
  
[boostRedis.cpp](https://github.com/user-attachments/files/24119733/boostRedis.cpp)  
  
/usr/bin/ld: /tmp/ccIKzGz4.o: in function `boost::redis::request::check_cmd(std::basic_string_view<char, std::char_traits<char> >)':  
boostRedis.cpp:(.text._ZN5boost5redis7request9check_cmdESt17basic_string_viewIcSt11char_traitsIcEE[_ZN5boost5redis7request9check_cmdESt17basic_string_viewIcSt11char_traitsIcEE]+0x5b): undefined reference to `boost::redis::detail::has_response(std::basic_string_view<char, std::char_traits<char> >)'  
/usr/bin/ld: /tmp/ccIKzGz4.o: in function `sync_connection::stop()::{lambda()#1}::operator()() const':  
boostRedis.cpp:(.text._ZZN15sync_connection4stopEvENKUlvE_clEv[_ZZN15sync_connection4stopEvENKUlvE_clEv]+0x2c): undefined reference to `boost::redis::connection::cancel(boost::redis::operation)'  
/usr/bin/ld: /tmp/ccIKzGz4.o: in function `boost::system::error_code::error_code<boost::redis::error>(boost::redis::error, std::enable_if<boost::system::is_error_code_enum<boost::redis::error>::value||std::is_error_code_enum<boost::redis::error>::value, void>::type*)':  
boostRedis.cpp:(.text._ZN5boost6system10error_codeC2INS_5redis5errorEEET_PNSt9enable_ifIXoosrNS0_18is_error_code_enumIS5_EE5valuesrSt18is_error_code_enumIS5_E5valueEvE4typeE[_ZN5boost6system10error_codeC5INS_5redis5errorEEET_PNSt9enable_ifIXoosrNS0_18is_error_code_enumIS5_EE5valuesrSt18is_error_code_enumIS5_E5valueEvE4typeE]+0x57): undefined reference to `boost::redis::make_error_code(boost::redis::error)'  
/usr/bin/ld: /tmp/ccIKzGz4.o: in function `void boost::redis::request::push<>(std::basic_string_view<char, std::char_traits<char> >)':  
boostRedis.cpp:(.text._ZN5boost5redis7request4pushIJEEEvSt17basic_string_viewIcSt11char_traitsIcEEDpRKT_[_ZN5boost5redis7request4pushIJEEEvSt17basic_string_viewIcSt11char_traitsIcEEDpRKT_]+0x56): undefined reference to `boost::redis::resp3::add_header(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, boost::redis::resp3::type, unsigned long)'  
/usr/bin/ld: /tmp/ccIKzGz4.o: in function `boost::redis::resp3::add_bulk_impl<std::basic_string_view<char, std::char_traits<char> > >::add(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::basic_string_view<char, std::char_traits<char> > const&)':  
boostRedis.cpp:(.text._ZN5boost5redis5resp313add_bulk_implISt17basic_string_viewIcSt11char_traitsIcEEE3addERNSt7__cxx1112basic_stringIcS5_SaIcEEERKS6_[_ZN5boost5redis5resp313add_bulk_implISt17basic_string_viewIcSt11char_traitsIcEEE3addERNSt7__cxx1112basic_stringIcS5_SaIcEEERKS6_]+0x2a): undefined reference to `boost::redis::resp3::boost_redis_to_bulk(std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >&, std::basic_string_view<char, std::char_traits<char> >)'  
/usr/bin/ld: /tmp/ccIKzGz4.o: in function `void std::_Construct<boost::redis::connection, boost::asio::io_context&>(boost::redis::connection*, boost::asio::io_context&)':  
boostRedis.cpp:(.text._ZSt10_ConstructIN5boost5redis10connectionEJRNS0_4asio10io_contextEEEvPT_DpOT0_[_ZSt10_ConstructIN5boost5redis10connectionEJRNS0_4asio10io_contextEEEvPT_DpOT0_]+0xa0): undefined reference to `boost::redis::connection::connection(boost::asio::io_context&, boost::asio::ssl::context, unsigned long)'  
/usr/bin/ld: /tmp/ccIKzGz4.o: in function `_ZSt12construct_atIN5boost5redis10connectionEJRNS0_4asio10io_contextEEQaant20is_unbounded_array_vIT_ErqXgsnwcvPvLi0E_S6_pispcl7declvalIT0_EEEEEPS6_S9_DpOS8_':  
boostRedis.cpp:(.text._ZSt12construct_atIN5boost5redis10connectionEJRNS0_4asio10io_contextEEQaant20is_unbounded_array_vIT_ErqXgsnwcvPvLi0E_S6_pispcl7declvalIT0_EEEEEPS6_S9_DpOS8_[_ZSt12construct_atIN5boost5redis10connectionEJRNS0_4asio10io_contextEEQaant20is_unbounded_array_vIT_ErqXgsnwcvPvLi0E_S6_pispcl7declvalIT0_EEEEEPS6_S9_DpOS8_]+0x7f): undefined reference to `boost::redis::connection::connection(boost::asio::io_context&, boost::asio::ssl::context, unsigned long)'  
/usr/bin/ld: /tmp/ccIKzGz4.o: in function `auto boost::redis::connection::async_exec<boost::asio::deferred_t const&>(boost::redis::request const&, boost::redis::any_adapter, boost::asio::deferred_t const&)::{lambda(auto:1, boost::redis::connection*, boost::redis::request const*, boost::redis::any_adapter&&)#1}::operator()<boost::asio::detail::promise_handler<void (boost::system::error_code, unsigned long), std::allocator<void> > >(boost::asio::detail::promise_handler<void (boost::system::error_code, unsigned long), std::allocator<void> >, boost::redis::connection*, boost::redis::request const*, boost::redis::any_adapter&&) const':  
boostRedis.cpp:(.text._ZZN5boost5redis10connection10async_execIRKNS_4asio10deferred_tEEEDaRKNS0_7requestENS0_11any_adapterEOT_ENKUlSB_PS1_PS8_OSA_E_clINS3_6detail15promise_handlerIFvNS_6system10error_codeEmESaIvEEEEEDaSB_SD_SE_SF_[_ZZN5boost5redis10connection10async_execIRKNS_4asio10deferred_tEEEDaRKNS0_7requestENS0_11any_adapterEOT_ENKUlSB_PS1_PS8_OSA_E_clINS3_6detail15promise_handlerIFvNS_6system10error_codeEmESaIvEEEEEDaSB_SD_SE_SF_]+0x6f): undefined reference to `boost::redis::connection::async_exec_impl(boost::redis::request const&, boost::redis::any_adapter&&, boost::asio::any_completion_handler<void (boost::system::error_code, unsigned long)>)'  
collect2: error: ld returned 1 exit status

---

## Comment 1

> Username: anarthal  
> Created at: 2025-12-12 10:50:23 UTC  
> Url: https://github.com/boostorg/redis/issues/371#issuecomment-3645970267  

You need to `#include <boost/redis/src.hpp>` in exactly one translation unit (i.e. cpp file) in your application. This should make the linker errors go away.

---

## Comment 2

> Username: dayashankerprasad  
> Created at: 2025-12-12 18:11:38 UTC  
> Url: https://github.com/boostorg/redis/issues/371#issuecomment-3647601687  

Thanks! It worked.

---

## Comment 3

> Username: anarthal  
> Created at: 2025-12-12 18:52:28 UTC  
> Url: https://github.com/boostorg/redis/issues/371#issuecomment-3647735507  

No problem! I'll close this issue now. Please feel free to open another one if you need more help.
