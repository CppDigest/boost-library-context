# #98 - Use traits more lazily [Closed]

> Username: mglisse  
> Created at: 2018-12-04 17:41:46 UTC  
> Updated at: 2019-03-04 18:32:35 UTC  
> Closed at: 2019-03-04 18:32:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/98  

Hello,  
  
I am debugging some code through occasional testsuite results so this won't be super specific, sorry.  
  
I notice a lot of eager use of traits (accessing `::value`) in Boost.Multiprecision. For instance  
  
```c++  
   template <class V>  
   BOOST_MP_FORCEINLINE BOOST_CONSTEXPR number(const V& v, typename boost::enable_if_c<  
            is_convertible<typename detail::canonical<V, Backend>::type, Backend>::value  
            && !detail::is_restricted_conversion<typename detail::canonical<V, Backend>::type, Backend>::value  
>::type* = 0)   
```  
Even if the type is not convertible, `is_restricted_conversion` is still going to be instantiated. In my case, with some recent visual studio, a call to an overloaded function on something that is not a number type at all ends up with an error in numeric_limits (through is_restricted_conversion, etc) and not the SFINAE I would have expected.  
  
If you replace `T1::value&&!T2::value` with `mpl::and_<T1,mpl::not_<T2>>::value`, I believe it might help (I can't easily check). It might also improve compilation time.  
  
I am just showing one place because this is what the error message points to, but the use of values instead of classes is everywhere in this library :disappointed: .

---

## Comment 1

> Username: mglisse  
> Created at: 2018-12-09 18:12:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/98#issuecomment-445558884  

```c++  
#include <boost/multiprecision/gmp.hpp>  
struct A{ virtual void g()=0; };  
void f(A&);  
void f(boost::multiprecision::mpq_rational);  
void h(A&a){ f(a); }  
```  
With g++ on linux, I get similar errors about `numeric_limits<A>`. Using MPL as described above helps for one of the constructors of `number`, but for the other one is_restricted_conversion is "naked", so it needs to be made safer. I am not sure what the exact meaning is supposed to be for all these traits, but maybe is_restricted_conversion could first check if there is a conversion at all and only then check if it is lossy (`mpl::and_<is_explicitly_convertible<From, To>, mpl::or_<mpl::not_<is_convertible<From, To>>, is_lossy_conversion<From, To>>>`).  
  
The original error happens for `operator*`, something that is quite heavily overloaded, so it seems important to make it as sfinae-friendly as possible (the type has boost numbers as template parameters, so the compiler does try functions in that namespace).

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-12-10 18:20:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/98#issuecomment-445917940  

Thanks for the test case - I'm testing a fix now, but there are still cases that will fail - for example if type A is incomplete - that particular case is probably unfixable though.
