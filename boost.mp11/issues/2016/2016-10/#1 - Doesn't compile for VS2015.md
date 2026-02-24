# #1 - Doesn't compile for VS2015 [Closed]

> Username: MikeMarcin  
> Created at: 2016-10-23 05:16:09 UTC  
> Updated at: 2016-10-23 15:33:57 UTC  
> Closed at: 2016-10-23 15:33:57 UTC  
> Url: https://github.com/boostorg/mp11/issues/1  

Trivial program doesn't compile with MSVC 2015 Update 3 and Boost 1.62.0  
  
`#include <boost/mp11.hpp>  
int main() {  
}`  
  
Did BOOST_NO_CXX11_CONSTEXPR change perhaps?  
  
> 1>C:\code\mp11\include\boost/mp11/algorithm.hpp(117): warning C4346: 'std::is_same<T,V>::value': dependent name is not a  
> 1>type  
> 1>  C:\code\mp11\include\boost/mp11/algorithm.hpp(117): note: prefix with 'typename' to indicate a type  
> 1>  C:\code\mp11\include\boost/mp11/algorithm.hpp(118): note: see reference to class template instantiation 'boost::detai  
> 1>  l::mp_count_impl<L<T...>,V>' being compiled  
> 1>C:\code\mp11\include\boost/mp11/algorithm.hpp(117): error C2059: syntax error: '...'  
> 1>C:\code\mp11\include\boost/mp11/algorithm.hpp(117): error C2976: 'boost::mp_size_t': too few template arguments  
> 1>  C:\code\mp11\include\boost/mp11/integral.hpp(30): note: see declaration of 'boost::mp_size_t'  
> 1>C:\code\mp11\include\boost/mp11/algorithm.hpp(143): warning C4346: 'std::integral_constant<bool,B>::value': dependent n  
> 1>ame is not a type  
> 1>  C:\code\mp11\include\boost/mp11/algorithm.hpp(143): note: prefix with 'typename' to indicate a type  
> 1>  C:\code\mp11\include\boost/mp11/algorithm.hpp(144): note: see reference to class template instantiation 'boost::detai  
> 1>  l::mp_count_if_impl<L<T...>,P>' being compiled  
> 1>C:\code\mp11\include\boost/mp11/algorithm.hpp(143): error C2059: syntax error: '...'  
> 1>C:\code\mp11\include\boost/mp11/algorithm.hpp(143): error C2976: 'boost::mp_size_t': too few template arguments  
> 1>  C:\code\mp11\include\boost/mp11/integral.hpp(30): note: see declaration of 'boost::mp_size_t'

---

## Comment 1

> Username: pdimov  
> Created at: 2016-10-23 12:43:25 UTC  
> Url: https://github.com/boostorg/mp11/issues/1#issuecomment-255586553  

Interesting. Works with Update 2.

---

## Comment 2

> Username: pdimov  
> Created at: 2016-10-23 12:49:33 UTC  
> Url: https://github.com/boostorg/mp11/issues/1#issuecomment-255586821  

Yes, it's the `BOOST_NO_CXX11_CONSTEXPR` macro; `visualc.hpp` line 185:  
  
```  
// C++11 features supported by VC++ 14 update 3 (aka 2015)  
//  
#if (_MSC_FULL_VER < 190024210)  
#  define BOOST_NO_CXX14_VARIABLE_TEMPLATES  
#  define BOOST_NO_SFINAE_EXPR  
#  define BOOST_NO_CXX11_CONSTEXPR  
#endif  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2016-10-23 15:20:08 UTC  
> Url: https://github.com/boostorg/mp11/issues/1#issuecomment-255594409  

Should be fixed now.

---

## Comment 4

> Username: MikeMarcin  
> Created at: 2016-10-23 15:33:57 UTC  
> Url: https://github.com/boostorg/mp11/issues/1#issuecomment-255595162  

It is, thanks!
