# #186 - implicit instantiation of undefined template 'boost::serialization::nvp<bool>' [Open]

> Username: zahiraam  
> Created at: 2019-12-16 15:59:54 UTC  
> Updated at: 2020-07-16 20:27:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/186  

Compiling "autodiff_mixed_partials.cpp with clang9+ gives an   
 "implicit instantiation of undefined template 'boost::serialization::nvp<bool>'"  
error.  
  
This is a reproducer:  
namespace boost  
{  
  namespace serialization  
  {  
    template < class > struct nvp;  
    template < class T > nvp < T > make_nvp (T);  
  }  
  template < int >class A  
  {  
    bool m_sign;  
    template < class > int serialize ()  
    {  
      boost:  
        serialization::make_nvp (m_sign);  
    }  
  };  
}  
This seems to be an issue of forward declaration.   
Any suggestions for fixing this issue. Tried to define serialize after struct nvp but got the same issue.

---

## Comment 1

> Username: correaa  
> Created at: 2019-12-16 18:02:46 UTC  
> Url: https://github.com/boostorg/serialization/issues/186#issuecomment-566173449  

In the current design of boost serialization, I think you cannot get away with simply forward define the `nvp` template class. You need to include the full header (class definition) and therefore have a hard dependency on `#include<boost/serialization/nvp.hpp` in your serialization code. So, you have to `#include<boost/serialization/nvp.hpp` before instantiating nvp for any specific type.  
  
I pointed out to this here: https://github.com/boostorg/serialization/issues/116  
and recently offered a possible solution to avoid the hard dependency: https://github.com/boostorg/serialization/issues/116#issuecomment-558895636

---

## Comment 2

> Username: robertramey  
> Created at: 2019-12-16 19:33:47 UTC  
> Url: https://github.com/boostorg/serialization/issues/186#issuecomment-566208881  

Right - try including boost/include/core/nvp.hpp - or something like that.

---

## Comment 3

> Username: correaa  
> Created at: 2019-12-17 18:04:41 UTC  
> Url: https://github.com/boostorg/serialization/issues/186#issuecomment-566679772  

Thanks @robertramey , do you have any opinion on https://github.com/boostorg/serialization/issues/116#issuecomment-558895636?

---

## Comment 4

> Username: correaa  
> Created at: 2019-12-31 11:38:56 UTC  
> Updated at: 2019-12-31 12:41:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/186#issuecomment-569915298  

@zahiraam BTW, I am pretty sure this is not a solution to your problem, but I think you are not properly forward-declaring the correct version of the `make_nvp` function. Such forward-declaration should look more like this:  
  
```c++  
namespace boost{  
namespace serialization{  
	template<class> struct nvp;  
	template<class T> const nvp<T> make_nvp(char const* name, T& t);  
//	template<class T> class array_wrapper;  
//	template<class T, class S> const array_wrapper<T> make_array(T* t, S s);  
}  
}  
```  
  
That is, you are missing the first argument of `make_nvp`. (This is the forward declaration I use for other purposes.)  
  
(I copied this declaration, from `/usr/include/boost/serialization/nvp.hpp`. I think this now has moved to `/usr/include/boost/core/nvp.hpp` or something like that.)

---

## Comment 5

> Username: abraxa  
> Created at: 2020-07-16 20:27:11 UTC  
> Url: https://github.com/boostorg/serialization/issues/186#issuecomment-659653064  

Hi, is this a bug in boost or a user error then? When using boost 1.71.0 with clang-10, my include "#include <boost/multiprecision/cpp_dec_float.hpp>" causes this:  
  
```  
/usr/include/boost/multiprecision/cpp_dec_float.hpp:613:12: error: implicit instantiation of undefined template 'boost::serialization::nvp<bool>'  
      ar & boost::serialization::make_nvp("sign", neg);  
           ^  
/usr/include/boost/multiprecision/detail/number_base.hpp:60:14: note: template is declared here  
      struct nvp;  
             ^  
```
