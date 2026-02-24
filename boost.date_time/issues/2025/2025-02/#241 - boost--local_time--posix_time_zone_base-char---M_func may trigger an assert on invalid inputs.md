# #241 - boost::local_time::posix_time_zone_base<char>::M_func may trigger an assert on invalid inputs [Open]

> Username: intosi  
> Created at: 2025-02-03 15:44:58 UTC  
> Updated at: 2025-02-03 15:47:15 UTC  
> Url: https://github.com/boostorg/date_time/issues/241  

We have an routine that parses date-time strings coming in over protocol. The code used is:  
  
```  
boost::posix_time::ptime stringToDateTime(const std::string& string) {  
        static std::locale locale(std::locale::classic(), new boost::local_time::local_time_input_facet("%Y-%m-%d %H:%M:%S%F%ZP"));  
        std::istringstream stream(string);  
        stream.imbue(locale);  
        boost::local_time::local_date_time result(boost::date_time::not_a_date_time);  
        stream >> result;  
        return result.utc_time();  
}  
```  
  
This normally works fine, but when AFL++ managed to generate input with the following timestamp: `20111111111111111111F,M,117-07-13:20:00:00Z`, boost asserted with the this backtrace:  
  
```  
    frame #0: 0x000000018e973720 libsystem_kernel.dylib`__pthread_kill + 8  
    frame #1: 0x000000018e9abf70 libsystem_pthread.dylib`pthread_kill + 288  
    frame #2: 0x000000018e8b8908 libsystem_c.dylib`abort + 128  
    frame #3: 0x000000018e8b7c1c libsystem_c.dylib`__assert_rtn + 284  
  * frame #4: 0x0000000100e3bbc8 checker`boost::token_iterator<boost::char_separator<char, std::__1::char_traits<char>>, std::__1::__wrap_iter<char const*>, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>>::increment(this=0x000000016fdfcda8) at token_iterator.hpp:51:11  
    frame #5: 0x0000000100e3bb64 checker`void boost::iterator_core_access::increment<boost::token_iterator<boost::char_separator<char, std::__1::char_traits<char>>, std::__1::__wrap_iter<char const*>, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>>>(f=0x000000016fdfcda8) at iterator_facade.hpp:520:13  
    frame #6: 0x0000000100e3ba80 checker`boost::iterator_facade<boost::token_iterator<boost::char_separator<char, std::__1::char_traits<char>>, std::__1::__wrap_iter<char const*>, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>>, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, boost::forward_traversal_tag, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long>::operator++(this=0x000000016fdfcda8) at iterator_facade.hpp:660:11  
    frame #7: 0x0000000100e388cc checker`boost::detail::postfix_increment_result<boost::token_iterator<boost::char_separator<char, std::__1::char_traits<char>>, std::__1::__wrap_iter<char const*>, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>>, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, boost::forward_traversal_tag>::type boost::operator++<boost::token_iterator<boost::char_separator<char, std::__1::char_traits<char>>, std::__1::__wrap_iter<char const*>, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>>, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, boost::forward_traversal_tag, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long>(i=0x000000016fdfcda8, (null)=0) at iterator_facade.hpp:730:7  
    frame #8: 0x0000000100e3d2c0 checker`boost::local_time::posix_time_zone_base<char>::M_func(this=0x00006000000a83c0, s="M", e="117-07-13:20:00:00Z") at posix_time_zone.hpp:393:42  
    frame #9: 0x0000000100e38aa4 checker`boost::local_time::posix_time_zone_base<char>::calc_rules(this=0x00006000000a83c0, start="M", end="117-07-13:20:00:00Z") at posix_time_zone.hpp:332:9  
    frame #10: 0x0000000100e37ca4 checker`boost::local_time::posix_time_zone_base<char>::posix_time_zone_base(this=0x00006000000a83c0, s="1F,M,117-07-13:20:00:00Z") at posix_time_zone.hpp:115:9  
    frame #11: 0x0000000100e31760 checker`boost::local_time::posix_time_zone_base<char>::posix_time_zone_base(this=0x00006000000a83c0, s="1F,M,117-07-13:20:00:00Z") at posix_time_zone.hpp:92:5  
    frame #12: 0x0000000100e2d530 checker`std::__1::basic_istream<char, std::__1::char_traits<char>>& boost::local_time::operator>><char, std::__1::char_traits<char>>(is=0x000000016fdfd9b0, ldt=0x000000016fdfd990) at local_time_io.hpp:91:36  
```  
  
It looks like the iterator increments in `boost::local_time::posix_time_zone_base<char>::M_func` are performed blindly, which means the code hits the `BOOST_ASSERT` in the token iterator that detects increments of invalid iterators, instead of returning a `not_a_date_time`, or (if enabled with `stream::exceptions`) throwing an exception.
