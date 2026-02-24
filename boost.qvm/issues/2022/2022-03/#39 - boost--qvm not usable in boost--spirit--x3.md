# #39 - boost::qvm not usable in boost::spirit::x3 [Closed]

> Username: octopus-prime  
> Created at: 2022-03-27 11:50:03 UTC  
> Updated at: 2022-04-15 18:12:27 UTC  
> Closed at: 2022-04-15 18:12:27 UTC  
> Url: https://github.com/boostorg/qvm/issues/39  

If you try to parse   
`boost::qvm::vec<float, 3>`   
you fail with compiler errors:  
  
`error: conversion from 'boost::qvm::vec<float, 3>' to 'boost::spirit::x3::unused_type' is ambiguous`  
  
reason:  
  
```  
/usr/include/boost/qvm/vec.hpp:25:13: note: candidate function [with R = boost::spirit::x3::unused_type]  
  
            operator R() const  
  
            ^  
  
/usr/include/boost/spirit/home/x3/support/unused.hpp:21:9: note: candidate constructor [with T = boost::qvm::vec<float, 3>]  
  
        unused_type(T const&)  
  
        ^  
```

---

## Comment 1

> Username: zajo  
> Created at: 2022-03-27 20:54:53 UTC  
> Url: https://github.com/boostorg/qvm/issues/39#issuecomment-1080017272  

Can I get a complete short program that demonstrates the problem?

---

## Comment 2

> Username: octopus-prime  
> Created at: 2022-03-27 21:39:11 UTC  
> Updated at: 2022-03-27 21:40:00 UTC  
> Url: https://github.com/boostorg/qvm/issues/39#issuecomment-1080024325  

```  
#include <boost/qvm/vec.hpp>  
#include <boost/spirit/home/x3.hpp>  
  
namespace qvm = boost::qvm;  
namespace x3 = boost::spirit::x3;  
  
using scalar_t = float;  
  
auto const scalar = x3::rule<struct scalar_, scalar_t>{"scalar"} =  
x3::float_;  
  
using vector_t = qvm::vec<scalar_t, 3>;  
  
template <size_t N>  
struct foo {  
	template <typename C>  
	void operator()(C const& ctx) const {  
		x3::_val(ctx).a[N] = x3::_attr(ctx);  
	}  
};  
  
auto const vector = x3::rule<struct vector_, vector_t>{"vector"} =  
x3::lit('<') > scalar [foo<0>()] > x3::lit(',') > scalar [foo<1>()] > x3::lit(',') > scalar [foo<2>()] > x3::lit('>');  
  
int main() {  
    std::string input = "center = <0,0,0>";  
    vector_t result;  
    x3::phrase_parse(input.begin(), input.end(), x3::lit("center") > x3::lit('=') > vector > x3::eoi, x3::space, result);  
    return 0;  
}  
```  
  
Tip: use clang++ because of g++ 'internal compiler error'

---

## Comment 3

> Username: zajo  
> Created at: 2022-04-14 02:30:08 UTC  
> Url: https://github.com/boostorg/qvm/issues/39#issuecomment-1098648744  

Thanks. Please confirm that this fixes it: https://github.com/boostorg/qvm/tree/feature/issue_39.

---

## Comment 4

> Username: octopus-prime  
> Created at: 2022-04-15 07:41:10 UTC  
> Url: https://github.com/boostorg/qvm/issues/39#issuecomment-1099933817  

Works like a charm 👍
