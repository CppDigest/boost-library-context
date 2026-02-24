# #20 - fail to compile on linux OS [Closed]

> Username: wcy168  
> Created at: 2022-07-29 07:59:55 UTC  
> Updated at: 2022-07-30 00:03:33 UTC  
> Closed at: 2022-07-30 00:03:33 UTC  
> Url: https://github.com/boostorg/redis/issues/20  

I used aedis::resp3::request in two different header files, and the link failed.The following is the error message  
  
  
/usr/bin/ld: ./server.o: in function `aedis::adapter::detail::from_bulk(bool&, boost::basic_string_view<char, std::char_traits<char> >, boost::system::error_code&)':  
/usr/local/include/aedis/adapter/detail/adapters.hpp:68: multiple definition of `aedis::adapter::detail::from_bulk(bool&, boost::basic_string_view<char, std::char_traits<char> >, boost::system::error_code&)'; ./redis_client.o:/usr/local/include/aedis/adapter/detail/adapters.hpp:68: first defined here  
/usr/bin/ld: ./server.o: in function `aedis::adapter::detail::set_on_resp3_error(aedis::resp3::type, boost::system::error_code&)':  
/usr/local/include/aedis/adapter/detail/adapters.hpp:93: multiple definition of `aedis::adapter::detail::set_on_resp3_error(aedis::resp3::type, boost::system::error_code&)'; ./redis_client.o:/usr/local/include/aedis/adapter/detail/adapters.hpp:92: first defined here  
/usr/bin/ld: ./server.o: in function `aedis::adapt()':  
/usr/local/include/aedis/adapt.hpp:141: multiple definition of `aedis::adapt()'; ./redis_client.o:/usr/local/include/aedis/adapt.hpp:141: first defined here  
/usr/bin/ld: ./server.o: in function `aedis::adapter::detail::parse_double(char const*, unsigned long, boost::system::error_code&)':  
/usr/local/include/aedis/adapter/detail/adapters.hpp:42: multiple definition of `aedis::adapter::detail::parse_double(char const*, unsigned long, boost::system::error_code&)'; ./redis_client.o:/usr/local/include/aedis/adapter/detail/adapters.hpp:42: first defined here  
/usr/bin/ld: ./server.o: in function `aedis::adapter::detail::from_bulk(double&, boost::basic_string_view<char, std::char_traits<char> >, boost::system::error_code&)':  
/usr/local/include/aedis/adapter/detail/adapters.hpp:75: multiple definition of `aedis::adapter::detail::from_bulk(double&, boost::basic_string_view<char, std::char_traits<char> >, boost::system::error_code&)'; ./redis_client.o:/usr/local/include/aedis/adapter/detail/adapters.hpp:75: first defined here  
collect2: 错误：ld 返回 1

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-29 14:55:59 UTC  
> Url: https://github.com/boostorg/redis/issues/20#issuecomment-1199460204  

This is probably a misture of src.hpp. Does this comentou help you further https://github.com/mzimbres/aedis/issues/15#issuecomment-1194325177  
?

---

## Comment 2

> Username: wcy168  
> Created at: 2022-07-29 23:17:29 UTC  
> Url: https://github.com/boostorg/redis/issues/20#issuecomment-1200021472  

But it can be compiled in windows

---

## Comment 3

> Username: wcy168  
> Created at: 2022-07-30 00:03:25 UTC  
> Url: https://github.com/boostorg/redis/issues/20#issuecomment-1200041619  

Now it works normally. I made a mistake and an obsolete file was introduced into the compilation
