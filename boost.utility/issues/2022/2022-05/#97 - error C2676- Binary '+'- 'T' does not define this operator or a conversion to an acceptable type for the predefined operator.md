# #97 - error C2676: Binary '+': 'T' does not define this operator or a conversion to an acceptable type for the predefined operator [Open]

> Username: SRAWAS  
> Created at: 2022-05-02 08:29:44 UTC  
> Updated at: 2022-05-02 16:40:41 UTC  
> Url: https://github.com/boostorg/utility/issues/97  

I have this error when i compile my project using boost 1_79_0:  
  
error C2676: Binary '+': 'T' does not define this operator or a conversion to an acceptable type for the predefined operator in operators.hpp boost:  
  
Here is the problem:  
  
operators.hpp  
  
template<typename X,typename Y> struct add_typeof_helper          
{  
    BOOST_TYPEOF_NESTED_TYPEDEF_TPL(nested, (typeof_::make<X>() + typeof_::make<Y>()))  
    typedef typename nested::type type;  
};  
  
I use visual studio 2017,  language : C++ /std:c++latest  
  
Thank you in advance for your comments !

---

## Comment 1

> Username: Lastique  
> Created at: 2022-05-02 16:40:40 UTC  
> Url: https://github.com/boostorg/utility/issues/97#issuecomment-1115107160  

I think it will be difficult to diagnose without a reproducer code sample.
