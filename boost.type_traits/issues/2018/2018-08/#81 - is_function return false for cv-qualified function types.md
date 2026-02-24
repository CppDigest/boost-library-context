# #81 - is_function return false for cv-qualified function types [Closed]

> Username: jzmaddock  
> Created at: 2018-08-02 11:35:55 UTC  
> Updated at: 2019-01-08 09:09:08 UTC  
> Closed at: 2019-01-08 09:09:08 UTC  
> Url: https://github.com/boostorg/type_traits/issues/81  

In fact it flat out fails to compile with msvc.  
  
```  
#include <boost/type_traits/is_function.hpp>  
#include <boost/core/lightweight_test_trait.hpp>  
  
struct X  
{  
    void f() {}  
    void fc() const {}  
    void fv() volatile {}  
    void fcv() const volatile {}  
};  
  
template< class C, class F > void test( F C::* )  
{  
    BOOST_TEST_TRAIT_TRUE(( boost::is_function< F > ));  
}  
  
int main()  
{  
    test( &X::f );  
    test( &X::fc );  
    test( &X::fv );  
    test( &X::fcv );  
  
    return boost::report_errors();  
}  
```  
  
Moved from https://svn.boost.org/trac10/ticket/10934

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-01-08 09:09:08 UTC  
> Url: https://github.com/boostorg/type_traits/issues/81#issuecomment-452225522  

Fixed for conforming compilers.
