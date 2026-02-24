# #19 - Operators example uses class in global namespace [Closed]

> Username: daixtrose  
> Created at: 2017-05-10 20:55:14 UTC  
> Updated at: 2018-06-11 17:16:18 UTC  
> Closed at: 2017-05-11 00:07:02 UTC  
> Url: https://github.com/boostorg/yap/issues/19  

I refer to this example:  
  
```cpp  
template <boost::yap::expr_kind Kind, typename Tuple>  
struct lazy_vector_expr  
{  
    static const boost::yap::expr_kind kind = Kind;  
  
    Tuple elements;  
  
    BOOST_YAP_USER_BINARY_OPERATOR_MEMBER(plus, ::lazy_vector_expr)  
    BOOST_YAP_USER_BINARY_OPERATOR_MEMBER(minus, ::lazy_vector_expr)  
  
    // Note that this does not return an expression; it is greedily evaluated.  
    auto operator[] (std::size_t n) const;  
};  
```  
  
I find it odd that ```lazy_vector_expr``` needs to be prepended with ```::```. Won't ADL grab the surrounding class anyway? If not: could you add an example for a class in a namespace?

---

## Comment 1

> Username: tzlaine  
> Created at: 2017-05-11 00:07:02 UTC  
> Url: https://github.com/boostorg/yap/issues/19#issuecomment-300646151  

Try removing the explicit scoping and see what happens.  *Spoiler alert!*  Everything breaks.  Inside the lexical scope of lazy_vector_expr, lazy_vector_expr is a type -- but we need a template, which ::lazy_vector_expr is.  If it were in namespace foo, we'd have to write foo::lazy_vector_expr.
