# #76 - operator macros should define constexpr functions [Closed]

> Username: rgrover  
> Created at: 2018-03-09 22:03:24 UTC  
> Updated at: 2018-06-11 17:16:21 UTC  
> Closed at: 2018-03-10 01:11:31 UTC  
> Url: https://github.com/boostorg/yap/issues/76  

The likes of the following should be possible:  
  
```C++  
  
template <expr_kind Kind, typename T>  
struct m_expr  
{  
    constexpr static expr_kind kind = Kind;  
  
    using tuple_type = boost::hana::tuple<T>;  
  
    tuple_type elements;  
};  
  
BOOST_YAP_USER_BINARY_OPERATOR(plus, m_expr, expression)  
  
constexpr auto e = make_terminal<m_expr>(1) + make_terminal<m_expr>(1);  
```
