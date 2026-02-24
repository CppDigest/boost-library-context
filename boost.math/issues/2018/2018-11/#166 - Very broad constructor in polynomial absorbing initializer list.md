# #166 - Very broad constructor in polynomial absorbing initializer list [Closed]

> Username: NAThompson  
> Created at: 2018-11-28 20:32:42 UTC  
> Updated at: 2018-11-30 19:33:02 UTC  
> Closed at: 2018-11-30 19:33:02 UTC  
> Url: https://github.com/boostorg/math/issues/166  

I'm confused about the following constructor in `polynomial.hpp`:  
  
```cpp  
   template <class U>  
   explicit polynomial(const U& point)  
   {  
       if (point != U(0))  
          m_data.push_back(point);  
   }  
```  
  
This seems to be very broad, and have been called in surprising situations resulting in the [build breaking](https://ci.appveyor.com/project/jzmaddock/math/builds/20531621/job/6iqc63j7rb17h820). For instance, the following test:  
  
```cpp  
polynomial<double> c{{10, -6, -4, 3}};  
```  
  
which you would hope gets passed to the initializer list constructor, in fact gets passed to the aforementioned constructor and causes the following error:  
  
```bash  
test_polynomial.cpp(88) : error C2440: 'initializing' : cannot convert from 'initializer-list' to 'boost::math::tools::polynomial<double>'  
        Constructor for class 'boost::math::tools::polynomial<double>' is declared 'explicit'  
test_polynomial.cpp(89) : error C2440: 'initializing' : cannot convert from 'initializer-list' to 'boost::math::tools::polynomial<double>'  
```  
  
So I would like to know what the purpose of this constructor is and its use case.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-11-29 08:56:27 UTC  
> Url: https://github.com/boostorg/math/issues/166#issuecomment-442755237  

I'm not sure it's that useful, it constructs a single term polynomial, the constructor should be changed to:  
  
```  
   template <class U>  
   explicit polynomial(const U& point, typename boost::enable_if<boost::is_convertible<U, T> >::type* = 0)  
```  
  
I won't make the change because I know you have pending changes to that file and it'll possibly complicate your PR's, but if you want to commit that fix please do go ahead.  
  
Aside: we should probably have another overload:  
  
```  
   template <class U>  
   explicit polynomial(const U& range, typename boost::enable_if<boost::is_iterable<U, T> >::type* = 0)  
: polynomial(std::begin(range), std::end(range)){}  
```  
  
For some fictional trait "is_iterable" - it is implementable, but we don't have it right now.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-11-29 11:08:10 UTC  
> Url: https://github.com/boostorg/math/issues/166#issuecomment-442795327  

It needs a very up to date compiler, but:  
  
```  
#include <boost/type_traits/is_detected.hpp>  
  
template<class T>  
using begin_t = decltype(begin(std::declval<const T&>()));  
template<class T>  
using end_t = decltype(end(std::declval<const T&>()));  
template<class T>  
using const_iterator_t = typename T::const_iterator;  
  
template <class T>  
struct is_const_iterable  
   : public boost::integral_constant<bool,  
      boost::is_detected<begin_t, T>::value  
      && boost::is_detected<end_t, T>::value  
      && boost::is_detected<const_iterator_t, T>::value  
   > {};  
  
static_assert(is_const_iterable<std::vector<int> >::value);  
```

---

## Comment 3

> Username: NAThompson  
> Created at: 2018-11-29 22:37:40 UTC  
> Updated at: 2018-11-30 05:52:07 UTC  
> Url: https://github.com/boostorg/math/issues/166#issuecomment-443020155  

> I won't make the change because I know you have pending changes to that file and it'll possibly complicate your PR's, but if you want to commit that fix please do go ahead.  
  
I have no unmerged changes to the file now, so if you find a minute to drop that fix, go ahead and if I find a minute I'll do it. Another option is simply `[[deprecated]]`, since it appears to not achieve very much.  
  
  
Your range constructor seems far more useful to me, but it sounds like you'll need to prepare for a pitched battle against the CI system.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-11-30 19:33:02 UTC  
> Url: https://github.com/boostorg/math/issues/166#issuecomment-443314349  

See https://github.com/boostorg/math/pull/167
