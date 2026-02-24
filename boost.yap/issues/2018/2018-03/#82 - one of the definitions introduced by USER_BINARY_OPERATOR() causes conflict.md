# #82 - one of the definitions introduced by USER_BINARY_OPERATOR() causes conflict [Closed]

> Username: rgrover  
> Created at: 2018-03-19 04:59:11 UTC  
> Updated at: 2018-06-11 17:16:21 UTC  
> Closed at: 2018-03-22 03:31:52 UTC  
> Url: https://github.com/boostorg/yap/issues/82  

I've got two distinct expr templates (say expr1 and expr2) for which I need to support the use of the same binary operator leading to the same resulting expr_template.   
  
So I introduce the following in my code:  
  
```c++  
BOOST_YAP_USER_BINARY_OPERATOR(multiplies, expr1, resulting_expr)  
BOOST_YAP_USER_BINARY_OPERATOR(multiplies, expr2, resulting_expr)  
```  
  
Such a setup leads the compiler to give an error about  multiple definitions of the same operator overload for the following operator:  
  
```C++  
    template<typename T, typename Expr>                                        \  
    constexpr auto operator BOOST_YAP_INDIRECT_CALL(op_name)(T && lhs, Expr & rhs) \  
        ->::boost::yap::detail::free_binary_op_result_t<                       \  
            result_expr_template,                                              \  
            ::boost::yap::expr_kind::op_name,                                  \  
            T,                                                                 \  
            Expr &>                                                            \  
    {                                                                          \  
```     
  
As you can see, the above overload doesn't depend on the macro argument `expr_template`
