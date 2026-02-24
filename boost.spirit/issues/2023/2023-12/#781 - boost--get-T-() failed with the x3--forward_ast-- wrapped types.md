# #781 - boost::get<T>() failed with the x3::forward_ast<> wrapped types [Closed]

> Username: x0id  
> Created at: 2023-12-24 20:02:24 UTC  
> Updated at: 2025-05-09 22:23:08 UTC  
> Closed at: 2025-05-09 22:23:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/781  

The following code runs as expected:  
  
```cpp  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
namespace ast {  
  
struct op_1;  
  
struct expr_1 : x3::variant<  
    int,  
    x3::forward_ast<op_1>  
> {  
    using base_type::base_type;  
    using base_type::operator=;  
};  
  
struct op_1 {  
    expr_1 e;  
};  
  
struct op_2;  
  
typedef boost::variant<  
    int,  
    boost::recursive_wrapper<op_2>  
> expr_2;  
  
struct op_2 {  
    expr_2 e;  
};  
  
}  
  
void fun() {  
    ast::expr_1 ex1(1);  
    ast::expr_2 ex2(2);  
    ast::op_1 op1; op1.e = ex1;  
    ast::op_2 op2; op2.e = ex2;  
    ast::expr_1 ex1a(op1);  
    ast::expr_2 ex2a(op2);  
  
    std::cout << boost::get<int>(ex1) << std::endl;  
    std::cout << boost::get<x3::forward_ast<ast::op_1>>(&ex1a) << std::endl;  
    std::cout << boost::get<int>(ex2) << std::endl;  
    std::cout << boost::get<ast::op_2>(&ex2a) << std::endl;  
}  
  
int main () {  
    fun();  
    return 0;  
}  
```  
  
But it fails to compile without `x3::forward_ast<>` wrapper in line 44.  
In contrast, original `boost::variant` does not require to specify `boost::recursive_wrapper<>` in the similar `boost::get<>()` call (line 46).  
  
Coliru link: https://coliru.stacked-crooked.com/a/f52d436f462056ff

---

## Comment 1

> Username: intractabilis  
> Created at: 2024-04-24 20:00:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/781#issuecomment-2075740242  

`boost::get` is implemented by Boost Variant library. It knows about `boost::recursive_wrapper`:  
```cpp  
template <class VariantElement, class T>  
struct variant_element_functor :  
    boost::mpl::or_<  
        boost::is_same<VariantElement, T>,  
        boost::is_same<VariantElement, boost::recursive_wrapper<T> >,  
        boost::is_same<VariantElement, T& >  
    >  
{};  
```  
But it has no clue about `boost::spirit::x3::forward_ast`. I don't think Boost Spirit can do something about it.
