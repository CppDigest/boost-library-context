# #273 Classic: Fixed `std::complex` usage without the include [Merged]

> Username: Kojoley  
> Created at: 2017-11-12 14:07:30 UTC  
> Updated at: 2017-11-12 16:43:37 UTC  
> Merged at: 2017-11-12 14:07:39 UTC  
> Closed at: 2017-11-12 14:07:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/273  

08b336d (#195) removed forward declaration but did not add the include.  
  
```  
In file included from ../libs/spirit/classic/test/negated_eps_p_test.cpp:12:  
In file included from ../boost/spirit/include/phoenix1.hpp:11:  
In file included from ../boost/spirit/home/classic/phoenix.hpp:18:  
../boost/spirit/home/classic/phoenix/special_ops.hpp:62:48: error: no member named 'complex' in namespace 'std'  
template <typename T> struct rank<PHOENIX_STD::complex<T> >  
                                  ~~~~~~~~~~~~~^  
../boost/spirit/home/classic/phoenix/special_ops.hpp:62:56: error: 'T' does not refer to a value  
template <typename T> struct rank<PHOENIX_STD::complex<T> >  
                                                       ^  
../boost/spirit/home/classic/phoenix/special_ops.hpp:62:20: note: declared here  
template <typename T> struct rank<PHOENIX_STD::complex<T> >  
                   ^  
2 errors generated.  
```  
  
Closes https://svn.boost.org/trac10/ticket/13240

---
