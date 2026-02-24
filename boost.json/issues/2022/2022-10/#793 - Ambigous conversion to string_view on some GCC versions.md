# #793 - Ambigous conversion to string_view on some GCC versions [Closed]

> Username: grisumbras  
> Created at: 2022-10-27 16:40:11 UTC  
> Updated at: 2022-11-06 11:05:53 UTC  
> Closed at: 2022-11-06 11:05:53 UTC  
> Url: https://github.com/boostorg/json/issues/793  

```  
/boost/json/impl/serializer.ipp:306:41: error: call of overloaded 'basic_string_view(const boost::json::string&)' is ambiguous  
             string_view>(jv.get_string());  
                                         ^  
In file included from ./boost/json/string_view.hpp:14,  
                 from ./boost/json/kind.hpp:14,  
                 from ./boost/json/array.hpp:14,  
                 from ./boost/json.hpp:15,  
                 from ./boost/json/src.hpp:27,  
                 from libs/json/src/src.cpp:10:  
./boost/core/detail/string_view.hpp:395:5: note: candidate: 'boost::core::basic_string_view<Ch>::basic_string_view(const std::basic_string_view<Ch, std::char_traits<_CharT> >&) [with Ch = char]'  
     basic_string_view( std::basic_string_view<Ch, std::char_traits<Ch> > const& str ) BOOST_NOEXCEPT: p_( str.data() ), n_( str.size() )  
     ^~~~~~~~~~~~~~~~~  
./boost/core/detail/string_view.hpp:339:26: note: candidate: 'constexpr boost::core::basic_string_view<char>::basic_string_view(const boost::core::basic_string_view<char>&)'  
 template<class Ch> class basic_string_view  
                          ^~~~~~~~~~~~~~~~~  
```  
  
That is, instead of using `string::operator string_view`, the compiler tries picking one of the `string_view` constructors, which are equally good.  
  
Only appears to be a problem on GCC 8.3, and obviously requires C++17 enabled.

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-11-06 11:05:52 UTC  
> Url: https://github.com/boostorg/json/issues/793#issuecomment-1304775158  

This doesn't seem to have a solution. GCC 8.3 is just a bit too broken.
