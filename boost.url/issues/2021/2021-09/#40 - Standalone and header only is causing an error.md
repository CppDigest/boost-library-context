# #40 - Standalone and header only is causing an error [Closed]

> Username: matthijs  
> Created at: 2021-09-06 14:48:08 UTC  
> Updated at: 2021-09-14 04:47:17 UTC  
> Closed at: 2021-09-14 04:47:17 UTC  
> Url: https://github.com/boostorg/url/issues/40  

I get an error when compiling standalone and header only:  
```c++  
#define BOOST_URL_STANDALONE  
#define BOOST_URL_HEADER_ONLY  
#include <boost/url/url.hpp>  
  
int main(int argc, char** argv) {  
  return 0;  
}  
```  
  
The error I get:  
```  
In file included from /home/matthijs/t6/main.cpp:9:  
In file included from /usr/local/include/boost/url/url.hpp:14:  
In file included from /usr/local/include/boost/url/url_view.hpp:14:  
In file included from /usr/local/include/boost/url/detail/parts.hpp:14:  
/usr/local/include/boost/url/detail/char_type.hpp:124:16: error: no viable overloaded '='  
            ec = error::incomplete_pct_encoding;  
            ~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:77:9: note: candidate template ignored: requirement 'is_error_code_enum<boost::urls::error>::value' was not satisfied [with ErrorCodeEnum = boost::urls::error]  
        operator=( ErrorCodeEnum val ) BOOST_NOEXCEPT  
        ^  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:38:7: note: candidate function (the implicit copy assignment operator) not viable: no known conversion from 'boost::urls::error' to 'const boost::system::error_code' for 1st argument  
class error_code  
      ^  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:38:7: note: candidate function (the implicit move assignment operator) not viable: no known conversion from 'boost::urls::error' to 'boost::system::error_code' for 1st argument  
In file included from /home/matthijs/t6/main.cpp:9:  
In file included from /usr/local/include/boost/url/url.hpp:14:  
In file included from /usr/local/include/boost/url/url_view.hpp:14:  
In file included from /usr/local/include/boost/url/detail/parts.hpp:14:  
/usr/local/include/boost/url/detail/char_type.hpp:130:16: error: no viable overloaded '='  
            ec = error::bad_pct_encoding_digit;  
            ~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:77:9: note: candidate template ignored: requirement 'is_error_code_enum<boost::urls::error>::value' was not satisfied [with ErrorCodeEnum = boost::urls::error]  
        operator=( ErrorCodeEnum val ) BOOST_NOEXCEPT  
        ^  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:38:7: note: candidate function (the implicit copy assignment operator) not viable: no known conversion from 'boost::urls::error' to 'const boost::system::error_code' for 1st argument  
class error_code  
      ^  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:38:7: note: candidate function (the implicit move assignment operator) not viable: no known conversion from 'boost::urls::error' to 'boost::system::error_code' for 1st argument  
In file included from /home/matthijs/t6/main.cpp:9:  
In file included from /usr/local/include/boost/url/url.hpp:14:  
In file included from /usr/local/include/boost/url/url_view.hpp:14:  
In file included from /usr/local/include/boost/url/detail/parts.hpp:14:  
/usr/local/include/boost/url/detail/char_type.hpp:150:24: error: no viable overloaded '='  
                    ec = error::illegal_reserved_char;  
                    ~~ ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:77:9: note: candidate template ignored: requirement 'is_error_code_enum<boost::urls::error>::value' was not satisfied [with ErrorCodeEnum = boost::urls::error]  
        operator=( ErrorCodeEnum val ) BOOST_NOEXCEPT  
        ^  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:38:7: note: candidate function (the implicit copy assignment operator) not viable: no known conversion from 'boost::urls::error' to 'const boost::system::error_code' for 1st argument  
class error_code  
      ^  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:38:7: note: candidate function (the implicit move assignment operator) not viable: no known conversion from 'boost::urls::error' to 'boost::system::error_code' for 1st argument  
In file included from /home/matthijs/t6/main.cpp:9:  
In file included from /usr/local/include/boost/url/url.hpp:14:  
In file included from /usr/local/include/boost/url/url_view.hpp:993:  
In file included from /usr/local/include/boost/url/impl/url_view.ipp:15:  
/usr/local/include/boost/url/detail/parse.hpp:133:15: error: invalid operands to binary expression ('boost::urls::error_code' (aka 'boost::system::error_code') and 'boost::urls::error')  
        if(ec != error::no_match)  
           ~~ ^  ~~~~~~~~~~~~~~~  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_code.hpp:171:36: note: candidate function not viable: no known conversion from 'boost::urls::error' to 'const boost::system::error_code' for 2nd argument  
BOOST_SYSTEM_CONSTEXPR inline bool operator!=( const error_code & lhs, const error_code & rhs ) BOOST_NOEXCEPT  
                                   ^  
/home/matthijs/boost/boost_1_76_0/boost/system/error_code.hpp:35:13: note: candidate function not viable: no known conversion from 'boost::urls::error' to 'const boost::system::error_condition' for 2nd argument  
inline bool operator!=( const error_code & lhs, const error_condition & rhs ) BOOST_NOEXCEPT  
            ^  
/home/matthijs/boost/boost_1_76_0/boost/system/detail/error_condition.hpp:154:36: note: candidate function not viable: no known conversion from 'boost::urls::error_code' (aka 'boost::system::error_code') to 'const boost::system::error_condition' for 1st argument  
BOOST_SYSTEM_CONSTEXPR inline bool operator!=( const error_condition & lhs, const error_condition & rhs ) BOOST_NOEXCEPT  
                                   ^  
/home/matthijs/boost/boost_1_76_0/boost/system/error_code.hpp:45:13: note: candidate function not viable: no known conversion from 'boost::urls::error_code' (aka 'boost::system::error_code') to 'const boost::system::error_condition' for 1st argument  
inline bool operator!=( const error_condition & lhs, const error_code & rhs ) BOOST_NOEXCEPT  
            ^  
/home/matthijs/boost/boost_1_76_0/boost/utility/string_view.hpp:397:39: note: candidate template ignored: could not match 'basic_string_view<type-parameter-0-0, type-parameter-0-1>' against 'boost::system::error_code'  
    inline BOOST_CXX14_CONSTEXPR bool operator!=(basic_string_view<charT, traits> x,  
                                      ^  
/home/matthijs/boost/boost_1_76_0/boost/utility/string_view.hpp:457:39: note: candidate template ignored: could not match 'basic_string_view<type-parameter-0-0, type-parameter-0-1>' against 'boost::system::error_code'  
    inline BOOST_CXX14_CONSTEXPR bool operator!=(basic_string_view<charT, traits> x,  
                                      ^  
/home/matthijs/boost/boost_1_76_0/boost/utility/string_view.hpp:463:39: note: candidate template ignored: could not match 'basic_string<type-parameter-0-0, type-parameter-0-1, type-parameter-0-2>' against 'boost::system::error_code'  
    inline BOOST_CXX14_CONSTEXPR bool operator!=(const std::basic_string<charT, traits, Allocator> & x,  
                                      ^  
/home/matthijs/boost/boost_1_76_0/boost/utility/string_view.hpp:469:39: note: candidate template ignored: could not match 'basic_string_view<type-parameter-0-0, type-parameter-0-1>' against 'boost::system::error_code'  
    inline BOOST_CXX14_CONSTEXPR bool operator!=(basic_string_view<charT, traits> x,  
                                      ^  
/home/matthijs/boost/boost_1_76_0/boost/utility/string_view.hpp:475:39: note: candidate template ignored: could not match 'const charT *' against 'boost::urls::error_code' (aka 'boost::system::error_code')  
    inline BOOST_CXX14_CONSTEXPR bool operator!=(const charT * x,  
                                      ^  
```  
  
Regards, Matthijs

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-09-06 14:52:53 UTC  
> Url: https://github.com/boostorg/url/issues/40#issuecomment-913706507  

Yeah, so - this library has not been officially released yet, and is still being worked on. Also, the standalone configuration is being removed.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2021-09-14 04:47:17 UTC  
> Url: https://github.com/boostorg/url/issues/40#issuecomment-918798023  

This is "fixed" in the sense that the standalone support has been removed, as it is no longer a planned feature. The build scripts should work though, and `url_view` is beta quality.
