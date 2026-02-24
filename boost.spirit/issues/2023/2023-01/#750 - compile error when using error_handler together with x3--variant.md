# #750 - compile error when using error_handler together with x3::variant [Closed]

> Username: zhuoqiang  
> Created at: 2023-01-14 03:53:48 UTC  
> Updated at: 2025-05-09 09:50:11 UTC  
> Closed at: 2025-05-09 09:50:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/750  

not sure why but if used together with `x3::variant` in one compilation unit in g++ 11.3, there is a weird compile error:  
  
```  
boost/spirit/home/x3/support/ast/variant.hpp: In instantiation of 'struct boost::spirit::x3::variant<>':  
/my/src/parser.hpp:173:33:   required from here  
boost/spirit/home/x3/support/ast/variant.hpp:131:15: error: wrong number of template arguments (0, should be at least 1)  
  131 |         using variant_type = boost::variant<Types...>;  
      |               ^~~~~~~~~~~~  
  
boost/variant/variant_fwd.hpp:282:53: note: provided for 'template<class T0, class ... TN> class boost::variant'  
  282 | template < BOOST_VARIANT_AUX_DECLARE_PARAMS > class variant;  
      |                                                     ^~~~~~~  
```  
  
could be workaround by change the [`error_reporting.hpp`](https://www.boost.org/doc/libs/1_81_0/boost/spirit/home/x3/support/utility/error_reporting.hpp)  
  
```cpp  
// typedef typename std::iterator_traits<Iterator>::value_type char_type;  
// std::basic_string<char_type> line{start, end};  
std::string line{start, end}; //  
err_out << x3::to_utf8(line) << std::endl;  
```  
  
or replace the `std::endl` to `'\n'`  
  
```cpp  
typedef typename std::iterator_traits<Iterator>::value_type char_type;  
std::string line{start, end};  
err_out << x3::to_utf8(line) << '\n';  
```  
  
here I propose we replace the `std::endl` with `'\n'` to avoid hitting potential compile bug

---

## Comment 1

> Username: saki7  
> Created at: 2025-05-09 09:50:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/750#issuecomment-2865909134  

Marking as invalid; the reason will be commented on the PR.
