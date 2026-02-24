# #43 - Inherited `variant` value constructor fails under MSVC [Closed]

> Username: pdimov  
> Created at: 2024-03-24 16:11:15 UTC  
> Updated at: 2024-05-18 19:08:32 UTC  
> Closed at: 2024-05-18 19:08:32 UTC  
> Url: https://github.com/boostorg/variant2/issues/43  

```  
#include <boost/variant2/variant.hpp>  
  
struct expr: boost::variant2::variant<bool, int, double>  
{  
    using variant::variant;  
};  
  
void f1()  
{  
    expr e1{ true };  
    expr e2{ 1 };  
    expr e3{ 1.0 };  
}  
  
void f2()  
{  
    expr e1( true );  
    expr e2( 1 );  
    expr e3( 1.0 );  
}  
```  
  
https://godbolt.org/z/eGK6qhhG3

---

## Comment 1

> Username: QuellaZhang  
> Created at: 2024-05-17 07:00:01 UTC  
> Url: https://github.com/boostorg/variant2/issues/43#issuecomment-2116884304  

@pdimov , this issue still exists on MSVC, I am building boost with this commit [1cc2d37fc606ca828f0e771dd60616244c1d0f53](https://github.com/boostorg/boost/commit/1cc2d37fc606ca828f0e771dd60616244c1d0f53).  
  
variant_derived_construct2.cpp  
libs\variant2\test\variant_derived_construct2.cpp(18): error C2440: 'initializing': cannot convert from 'initializer list' to 'expr'  
libs\variant2\test\variant_derived_construct2.cpp(18): note: 'expr::expr': no overloaded function could convert all the argument types  
libs\variant2\test\variant_derived_construct2.cpp(11): note: could be 'expr::expr(expr &&)'  
libs\variant2\test\variant_derived_construct2.cpp(18): note: 'expr::expr(expr &&)': cannot convert argument 1 from 'bool' to 'expr &&'  
libs\variant2\test\variant_derived_construct2.cpp(18): note: Reason: cannot convert from 'bool' to 'expr'  
libs\variant2\test\variant_derived_construct2.cpp(11): note: or       'expr::expr(const expr &)'  
libs\variant2\test\variant_derived_construct2.cpp(18): note: 'expr::expr(const expr &)': cannot convert argument 1 from 'bool' to 'const expr &'  
libs\variant2\test\variant_derived_construct2.cpp(18): note: Reason: cannot convert from 'bool' to 'const expr'  
libs\variant2\test\variant_derived_construct2.cpp(11): note: or       'expr::expr(void)'  
libs\variant2\test\variant_derived_construct2.cpp(18): note: 'expr::expr': function does not take 1 arguments  
libs\variant2\test\variant_derived_construct2.cpp(10): note: or       'expr::expr(void)', which inherits 'boost::variant2::variant<bool,int,double>::variant(void) noexcept(<expr>)' via base class 'boost::variant2::variant<bool,int,double>'  
libs\variant2\test\variant_derived_construct2.cpp(10): note: or       'expr::expr(U &&)', which inherits 'boost::variant2::variant<bool,int,double>::variant(U &&) noexcept(<expr>)' via base class 'boost::variant2::variant<bool,int,double>'  
libs\variant2\test\variant_derived_construct2.cpp(10): note: or       'expr::expr(boost::variant2::in_place_type_t<U>,A ...)', which inherits 'boost::variant2::variant<bool,int,double>::variant(boost::variant2::in_place_type_t<U>,A ...)' via base class 'boost::variant2::variant<bool,int,double>'  
libs\variant2\test\variant_derived_construct2.cpp(10): note: or       'expr::expr(boost::variant2::in_place_type_t<U>,std::initializer_list<V>,A ...)', which inherits 'boost::variant2::variant<bool,int,double>::variant(boost::variant2::in_place_type_t<U>,std::initializer_list<V>,A ...)' via base class 'boost::variant2::variant<bool,int,double>'  
libs\variant2\test\variant_derived_construct2.cpp(10): note: or       'expr::expr(boost::variant2::in_place_index_t<I>,A ...)', which inherits 'boost::variant2::variant<bool,int,double>::variant(boost::variant2::in_place_index_t<I>,A ...)' via base class 'boost::variant2::variant<bool,int,double>'  
libs\variant2\test\variant_derived_construct2.cpp(10): note: or       'expr::expr(boost::variant2::in_place_index_t<I>,std::initializer_list<V>,A ...)', which inherits 'boost::variant2::variant<bool,int,double>::variant(boost::variant2::in_place_index_t<I>,std::initializer_list<V>,A ...)' via base class 'boost::variant2::variant<bool,int,double>'  
libs\variant2\test\variant_derived_construct2.cpp(10): note: or       'expr::expr(const boost::variant2::variant<U...> &)', which inherits 'boost::variant2::variant<bool,int,double>::variant(const boost::variant2::variant<U...> &) noexcept(<expr>)' via base class 'boost::variant2::variant<bool,int,double>'  
libs\variant2\test\variant_derived_construct2.cpp(10): note: or       'expr::expr(boost::variant2::variant<U...> &&)', which inherits 'boost::variant2::variant<bool,int,double>::variant(boost::variant2::variant<U...> &&) noexcept(<expr>)' via base class 'boost::variant2::variant<bool,int,double>'  
libs\variant2\test\variant_derived_construct2.cpp(18): note: while trying to match the argument list '(bool)'

---

## Comment 2

> Username: pdimov  
> Created at: 2024-05-17 09:25:14 UTC  
> Url: https://github.com/boostorg/variant2/issues/43#issuecomment-2117125589  

What MSVC version is that, and with what options?

---

## Comment 3

> Username: pdimov  
> Created at: 2024-05-17 18:22:07 UTC  
> Url: https://github.com/boostorg/variant2/issues/43#issuecomment-2118161000  

Looks like [VS 2022 has reached `_MSC_VER=1940`](https://devblogs.microsoft.com/cppblog/msvc-toolset-minor-version-number-14-40-in-vs-2022-v17-10/) so I'll need to increase the workaround condition in https://github.com/boostorg/variant2/commit/f0acc9326ee54534eb31ec1636608dd01f232790 to `< 1950`.

---

## Comment 4

> Username: pdimov  
> Created at: 2024-05-18 19:08:32 UTC  
> Url: https://github.com/boostorg/variant2/issues/43#issuecomment-2118979571  

Should be fixed now.
