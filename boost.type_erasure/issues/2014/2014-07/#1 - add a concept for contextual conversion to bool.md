# #1 - add a concept for contextual conversion to bool [Open]

> Username: akrzemi1  
> Created at: 2014-07-25 13:13:33 UTC  
> Updated at: 2014-07-25 13:13:33 UTC  
> Url: https://github.com/boostorg/type_erasure/issues/1  

One concept that is often useful in many types is a contextual conversion to bool. The following expressions should be valid with the concept:  
  
```  
if (o) {}  
if (!o) {}  
template <typename O>  
bool test(O&& o)  { return o; }  
```  
  
Add a predefined concept that catches these requirement. A possible implementation:  
  
```  
namespace boost { namespace type_erasure {  
  
template<class T>  
struct testable  
{  
  static bool apply(const T& arg)  
  { return bool(arg); }  
};  
  
template<class T, class Base>  
struct concept_interface<testable<T>, Base, T> : Base  
{  
  explicit operator bool () const  
  { return call(testable<T>(), *this); }  
};  
}}  
```
