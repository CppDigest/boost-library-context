# #1060 - Lexical cast must be imported after boost JSON [Closed]

> Username: Tasty213  
> Created at: 2024-12-09 14:14:23 UTC  
> Updated at: 2024-12-17 10:01:59 UTC  
> Closed at: 2024-12-16 11:28:56 UTC  
> Url: https://github.com/boostorg/json/issues/1060  

Hi,  
  
Got a really strange compiler error that I think is something to do with ADL but i'm not particularly sure.  
  
This is an example use of the boost json library:  
```  
#define BOOST_TEST_DYN_LINK  
#include <boost/test/unit_test.hpp>  
#include <boost/test/data/test_case.hpp>  
#include <boost/json/value.hpp>  
  
#include <map>  
#include <boost/lexical_cast.hpp>  
#include <boost/json.hpp>  
  
BOOST_AUTO_TEST_SUITE(Tool2ConfigTest)  
  
BOOST_AUTO_TEST_CASE(LoadsFromJsonType)  
{  
    const boost::json::value sample_tool_config_json = {  
        {"type", "Tool"},  
        {"id", "LARS"}};  
  
    auto config = boost::json::value_to<std::map<std::string, std::string>>(sample_tool_config_json);  
  
    BOOST_TEST(config["type"] == "Tool");  
}  
  
BOOST_AUTO_TEST_SUITE_END()  
```  
but when compiled it throws an error:  
  
```  
⬢ [gsykes@toolbox CS]$ g++ -o test/boost_test/Configuration/Tool2_test.o -c -ansi -W -Wall -Wextra -Wno-non-virtual-dtor -std=c++23 -pthread -DNDEBUG -fPIC -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE=1 -ggdb -DBOOST_BIND_GLOBAL_PLACEHOLDERS test/boost_test/Configuration/Tool2_test.cpp  
In file included from /usr/include/boost/json/value_to.hpp:15,  
                 from /usr/include/boost/json.hpp:41,  
                 from test/boost_test/Configuration/Tool2_test.cpp:8:  
/usr/include/boost/json/detail/value_to.hpp: In instantiation of ‘std::insert_iterator<_Container> boost::json::detail::inserter(T&, boost::mp11::mp_int<0>) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >; boost::mp11::mp_int<0> = std::integral_constant<int, 0>]’:  
/usr/include/boost/json/detail/value_to.hpp:310:32:   required from ‘T boost::json::detail::value_to_impl(boost::json::value_to_tag<T>, const boost::json::value&, map_like_conversion_tag) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >]’  
/usr/include/boost/json/value_to.hpp:73:33:   required from ‘T boost::json::value_to(const value&) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >]’  
test/boost_test/Configuration/Tool2_test.cpp:18:76:   required from here  
/usr/include/boost/json/detail/value_to.hpp:120:37: error: call of overloaded ‘end(std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >&)’ is ambiguous  
  120 |     return std::inserter(target, end(target));  
      |                                  ~~~^~~~~~~~  
In file included from /usr/include/boost/range/functions.hpp:19,  
                 from /usr/include/boost/range/iterator_range_core.hpp:38,  
                 from /usr/include/boost/lexical_cast.hpp:30,  
                 from test/boost_test/Configuration/Tool2_test.cpp:7:  
/usr/include/boost/range/end.hpp:92:55: note: candidate: ‘constexpr typename boost::range_iterator<C>::type boost::range_adl_barrier::end(T&) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >; typename boost::range_iterator<C>::type = std::_Rb_tree<std::__cxx11::basic_string<char>, std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >, std::_Select1st<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, std::less<std::__cxx11::basic_string<char> >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >::iterator]’  
   92 | inline BOOST_DEDUCED_TYPENAME range_iterator<T>::type end( T& r )  
      |                                                       ^~~  
/usr/include/boost/range/end.hpp:104:61: note: candidate: ‘constexpr typename boost::range_iterator<const T>::type boost::range_adl_barrier::end(const T&) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >; typename boost::range_iterator<const T>::type = std::_Rb_tree<std::__cxx11::basic_string<char>, std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >, std::_Select1st<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, std::less<std::__cxx11::basic_string<char> >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >::const_iterator]’  
  104 | inline BOOST_DEDUCED_TYPENAME range_iterator<const T>::type end( const T& r )  
      |                                                             ^~~  
In file included from /usr/include/c++/13/string:53,  
                 from /usr/include/boost/test/utils/basic_cstring/bcs_char_traits.hpp:25,  
                 from /usr/include/boost/test/utils/basic_cstring/basic_cstring.hpp:21,  
                 from /usr/include/boost/test/detail/global_typedef.hpp:15,  
                 from /usr/include/boost/test/tools/detail/print_helper.hpp:20,  
                 from /usr/include/boost/test/utils/lazy_ostream.hpp:16,  
                 from /usr/include/boost/test/tools/context.hpp:19,  
                 from /usr/include/boost/test/test_tools.hpp:42,  
                 from /usr/include/boost/test/unit_test.hpp:18,  
                 from test/boost_test/Configuration/Tool2_test.cpp:2:  
/usr/include/c++/13/bits/range_access.h:74:5: note: candidate: ‘constexpr decltype (__cont.end()) std::end(_Container&) [with _Container = map<__cxx11::basic_string<char>, __cxx11::basic_string<char> >; decltype (__cont.end()) = _Rb_tree<__cxx11::basic_string<char>, pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> >, _Select1st<pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> > >, less<__cxx11::basic_string<char> >, allocator<pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> > > >::iterator]’  
   74 |     end(_Container& __cont) -> decltype(__cont.end())  
      |     ^~~  
/usr/include/c++/13/bits/range_access.h:85:5: note: candidate: ‘constexpr decltype (__cont.end()) std::end(const _Container&) [with _Container = map<__cxx11::basic_string<char>, __cxx11::basic_string<char> >; decltype (__cont.end()) = _Rb_tree<__cxx11::basic_string<char>, pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> >, _Select1st<pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> > >, less<__cxx11::basic_string<char> >, allocator<pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> > > >::const_iterator]’  
   85 |     end(const _Container& __cont) -> decltype(__cont.end())  
      |     ^~~  
```  
If i swap the order and import boost json then boost lexical cast the error doesn't appear:  
  
```  
#define BOOST_TEST_DYN_LINK  
#include <boost/test/unit_test.hpp>  
#include <boost/test/data/test_case.hpp>  
#include <boost/json/value.hpp>  
  
#include <map>  
#include <boost/json.hpp>  
#include <boost/lexical_cast.hpp>  
  
BOOST_AUTO_TEST_SUITE(Tool2ConfigTest)  
  
BOOST_AUTO_TEST_CASE(LoadsFromJsonType)  
{  
    const boost::json::value sample_tool_config_json = {  
        {"type", "Tool"},  
        {"id", "LARS"}};  
  
    auto config = boost::json::value_to<std::map<std::string, std::string>>(sample_tool_config_json);  
  
    BOOST_TEST(config["type"] == "Tool");  
}  
  
BOOST_AUTO_TEST_SUITE_END()  
```  
compiled with the command:  
```  
⬢ [gsykes@toolbox CS]$ g++ -o test/boost_test/Configuration/Tool2_test.o -c -ansi -W -Wall -Wextra -Wno-non-virtual-dtor -std=c++23 -pthread -DNDEBUG -fPIC -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE=1 -ggdb -DBOOST_BIND_GLOBAL_PLACEHOLDERS test/boost_test/Configuration/Tool2_test.cpp  
```  
  
My g++ version is `g++ (GCC) 13.3.1 20240522 (Red Hat 13.3.1-1)` and boost version   
My boost version is 1.81.0 acquired by:  
  
```  
⬢ [gsykes@toolbox CS]$ rpm --query --all --queryformat '%{NAME}.%{ARCH}.%{VERSION}: %{SUMMARY}\n' | grep boost  
boost-system.x86_64.1.81.0: Run-time component of boost system support library  
boost-chrono.x86_64.1.81.0: Run-time component of boost chrono library  
boost-thread.x86_64.1.81.0: Run-time component of boost thread library  
boost-regex.x86_64.1.81.0: Run-time component of boost regular expression library  
boost-context.x86_64.1.81.0: Run-time component of boost context switching library  
boost-atomic.x86_64.1.81.0: Run-time component of boost atomic library  
boost-filesystem.x86_64.1.81.0: Run-time component of boost filesystem library  
boost-python3.x86_64.1.81.0: Run-time component of boost python library for Python 3  
boost-date-time.x86_64.1.81.0: Run-time component of boost date-time library  
boost-container.x86_64.1.81.0: Run-time component of boost container library  
boost-json.x86_64.1.81.0: Run-time component of boost json library  
boost-wave.x86_64.1.81.0: Run-time component of boost C99/C++ preprocessing library  
boost-fiber.x86_64.1.81.0: Run-time component of boost fiber library  
boost-log.x86_64.1.81.0: Run-time component of boost logging library  
boost-coroutine.x86_64.1.81.0: Run-time component of boost coroutine library  
boost-graph.x86_64.1.81.0: Run-time component of boost graph library  
boost-locale.x86_64.1.81.0: Run-time component of boost locale library  
boost-type_erasure.x86_64.1.81.0: Run-time component of boost type erasure library  
boost-timer.x86_64.1.81.0: Run-time component of boost timer library  
boost-test.x86_64.1.81.0: Run-time component of boost test library  
boost-stacktrace.x86_64.1.81.0: Run-time component of boost stacktrace library  
boost-serialization.x86_64.1.81.0: Run-time component of boost serialization library  
boost-random.x86_64.1.81.0: Run-time component of boost random library  
boost-program-options.x86_64.1.81.0: Run-time component of boost program_options library  
boost-nowide.x86_64.1.81.0: Standard library functions with UTF-8 API on Windows  
boost-math.x86_64.1.81.0: Math functions for boost TR1 library  
boost-iostreams.x86_64.1.81.0: Run-time component of boost iostreams library  
boost-contract.x86_64.1.81.0: Run-time component of boost contract library  
boost-numpy3.x86_64.1.81.0: Run-time component of boost numpy library for Python 3  
boost.x86_64.1.81.0: The free peer-reviewed portable C++ source libraries  
boost-devel.x86_64.1.81.0: The Boost C++ headers and shared development libraries  
```  
  
  
Appreciate the example shown doesn't use lexical cast but it's the best reproducible example i could make, found this as part of a much larger project that uses lexical cast extensively.  
  
Thanks in advance  
George

---

## Comment 1

> Username: Tasty213  
> Created at: 2024-12-09 15:05:11 UTC  
> Updated at: 2024-12-09 15:05:34 UTC  
> Url: https://github.com/boostorg/json/issues/1060#issuecomment-2528265151  

Worth noting after applying this fix i got a simmilar issue with ADL getting confused about end() in boost python, that was solved by importing boost python after all other packages in any file it was used it, error log from that is shown below but suggests this issue might be better placed in another boost repo, but i'll leave it here as i'm out of my depth until someone lets me know where it should go.  
  
Error that was fixed by re-arranging boost python imports, not a reproducible one but could look into making one if it'd help but imagine it'll be the same as the previous one but importing boost/python as well.  
```  
g++ -o src/Configuration/Tool.os -c -ansi -W -Wall -Wextra -Wno-non-virtual-dtor -std=c++23 -pthread -DNDEBUG -fPIC -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE=1 -ggdb -DBOOST_BIND_GLOBAL_PLACEHOLDERS -DPYMOD -DBOOST_BIND_GLOBAL_PLACEHOLDERS -fPIC -I. -Iinclude -IApplication/include -I/usr/include/python3.12 src/Configuration/Tool.cpp  
In file included from /usr/include/boost/json/value_to.hpp:15,  
                 from /usr/include/boost/json.hpp:41,  
                 from include/Isotek/CS/Object.hpp:24,  
                 from include/Isotek/CS/Container.hpp:21,  
                 from include/Isotek/CS/State.hpp:17,  
                 from include/Isotek/CS/Configuration/Config.hpp:17,  
                 from include/Isotek/CS/Configuration/Tool.hpp:3,  
                 from src/Configuration/Tool.cpp:3:  
/usr/include/boost/json/detail/value_to.hpp: In instantiation of ‘std::insert_iterator<_Container> boost::json::detail::inserter(T&, boost::mp11::mp_int<0>) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >; boost::mp11::mp_int<0> = std::integral_constant<int, 0>]’:  
/usr/include/boost/json/detail/value_to.hpp:310:32:   required from ‘T boost::json::detail::value_to_impl(boost::json::value_to_tag<T>, const boost::json::value&, map_like_conversion_tag) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >]’  
/usr/include/boost/json/value_to.hpp:73:33:   required from ‘T boost::json::value_to(const value&) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >]’  
src/Configuration/Tool.cpp:15:37:   required from ‘void Isotek::CS::extract(const boost::json::object&, T&, std::string_view) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >; std::string_view = std::basic_string_view<char>]’  
src/Configuration/Tool.cpp:27:16:   required from here  
/usr/include/boost/json/detail/value_to.hpp:120:37: error: call of overloaded ‘end(std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >&)’ is ambiguous  
  120 |     return std::inserter(target, end(target));  
      |                                  ~~~^~~~~~~~  
In file included from /usr/include/boost/range/functions.hpp:19,  
                 from /usr/include/boost/range/iterator_range_core.hpp:38,  
                 from /usr/include/boost/lexical_cast.hpp:30,  
                 from /usr/include/boost/python/operators.hpp:19,  
                 from /usr/include/boost/python.hpp:49,  
                 from include/Isotek/CS/Container.hpp:18:  
/usr/include/boost/range/end.hpp:92:55: note: candidate: ‘constexpr typename boost::range_iterator<C>::type boost::range_adl_barrier::end(T&) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >; typename boost::range_iterator<C>::type = std::_Rb_tree<std::__cxx11::basic_string<char>, std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >, std::_Select1st<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, std::less<std::__cxx11::basic_string<char> >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >::iterator]’  
   92 | inline BOOST_DEDUCED_TYPENAME range_iterator<T>::type end( T& r )  
      |                                                       ^~~  
/usr/include/boost/range/end.hpp:104:61: note: candidate: ‘constexpr typename boost::range_iterator<const T>::type boost::range_adl_barrier::end(const T&) [with T = std::map<std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >; typename boost::range_iterator<const T>::type = std::_Rb_tree<std::__cxx11::basic_string<char>, std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> >, std::_Select1st<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > >, std::less<std::__cxx11::basic_string<char> >, std::allocator<std::pair<const std::__cxx11::basic_string<char>, std::__cxx11::basic_string<char> > > >::const_iterator]’  
  104 | inline BOOST_DEDUCED_TYPENAME range_iterator<const T>::type end( const T& r )  
      |                                                             ^~~  
In file included from /usr/include/c++/13/unordered_map:42,  
                 from /usr/include/c++/13/functional:63,  
                 from /usr/include/boost/config/no_tr1/functional.hpp:21,  
                 from /usr/include/boost/function/detail/prologue.hpp:14,  
                 from /usr/include/boost/function/function_template.hpp:13,  
                 from /usr/include/boost/function/detail/maybe_include.hpp:15,  
                 from /usr/include/boost/function/function0.hpp:11,  
                 from /usr/include/boost/python/errors.hpp:13,  
                 from /usr/include/boost/python/handle.hpp:11,  
                 from /usr/include/boost/python/args_fwd.hpp:10,  
                 from /usr/include/boost/python/args.hpp:10,  
                 from /usr/include/boost/python.hpp:11:  
/usr/include/c++/13/bits/range_access.h:74:5: note: candidate: ‘constexpr decltype (__cont.end()) std::end(_Container&) [with _Container = map<__cxx11::basic_string<char>, __cxx11::basic_string<char> >; decltype (__cont.end()) = _Rb_tree<__cxx11::basic_string<char>, pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> >, _Select1st<pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> > >, less<__cxx11::basic_string<char> >, allocator<pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> > > >::iterator]’  
   74 |     end(_Container& __cont) -> decltype(__cont.end())  
      |     ^~~  
/usr/include/c++/13/bits/range_access.h:85:5: note: candidate: ‘constexpr decltype (__cont.end()) std::end(const _Container&) [with _Container = map<__cxx11::basic_string<char>, __cxx11::basic_string<char> >; decltype (__cont.end()) = _Rb_tree<__cxx11::basic_string<char>, pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> >, _Select1st<pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> > >, less<__cxx11::basic_string<char> >, allocator<pair<const __cxx11::basic_string<char>, __cxx11::basic_string<char> > > >::const_iterator]’  
   85 |     end(const _Container& __cont) -> decltype(__cont.end())  
      |     ^~~  
scons: *** [src/Configuration/Tool.os] Error 1  
scons: building terminated because of errors  
```  
  
Merry christmas!  
George

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-12-14 09:25:38 UTC  
> Url: https://github.com/boostorg/json/issues/1060#issuecomment-2543021869  

Yeah, this is an unfortunate ambiguity between `std::end` and its dad `boost::end`. Probably the only way to solve this on JSON's end is to use `std::end` unconditionally, which breaks support for theoretical containers that don't have begin/end members, but do have related begin/end free functions in an associated namespace. Since such containers are indeed mostly theoretical, this seems to be the correct course of action.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-12-16 11:28:56 UTC  
> Url: https://github.com/boostorg/json/issues/1060#issuecomment-2545366426  

Lol, turns out this is a duplicate of #887, and was already fixed in Boost 1.83.0.

---

## Comment 4

> Username: Tasty213  
> Created at: 2024-12-17 10:01:57 UTC  
> Url: https://github.com/boostorg/json/issues/1060#issuecomment-2548007960  

Ta, we'll bump versions, thanks for the quick response
