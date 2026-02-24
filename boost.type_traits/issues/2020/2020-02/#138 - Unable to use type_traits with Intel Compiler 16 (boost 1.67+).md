# #138 - Unable to use type_traits with Intel Compiler 16 (boost 1.67+) [Open]

> Username: nehaljwani  
> Created at: 2020-02-15 16:55:30 UTC  
> Updated at: 2020-02-15 19:09:40 UTC  
> Url: https://github.com/boostorg/type_traits/issues/138  

Source Code:  
```  
#include <boost/lexical_cast.hpp>  
#include <string>  
#include <iostream>  
  
float getSomeNumber()  
{  
  return 10.0;  
}  
  
int main(int argc, char** argv)  
{  
   std::string mystring("Hello: " + boost::lexical_cast<std::string>(getSomeNumber());  
   std::cout << mystring << std::endl;  
   return 0;  
}  
```  
  
Intel Compiler 16:  
```  
 /boost/1.67.0/include/boost/type_traits/is_complete.hpp(45): warning #70: incomplete type is not allowed  
  
       ok_tag<sizeof(T)> check_is_complete(int);  
       detected during ....  
       ...  
  
/boost/1.67.0/include/boost/type_traits/is_complete.hpp(51): error: template instantiation resulted in unexpected function type of "boost::detail::ok_tag<1U> (int)" (the meaning of a name may have changed since the template declaration -- the type of the template is "boost::detail::ok_tag<sizeof(T)> (int)")  
       ...  
  
     detected during:   
        instantiation of "boost::detail::check_is_complete" based on template argument <void> at line 51  
        instantiation of class "boost::is_complete<T> [with T=void]" at line 484 of "/Path/to/boost/include/boost/type_traits/is_convertible.hpp"  
        ...  
        instantiation of "Target boost::lexical_cast<Target, Source>(const Source &) [with Target=std::string, Source=float]" at line 11 of "boostTest.cc"  
```

---

## Comment 1

> Username: nehaljwani  
> Created at: 2020-02-15 16:55:53 UTC  
> Updated at: 2020-02-15 16:56:05 UTC  
> Url: https://github.com/boostorg/type_traits/issues/138#issuecomment-586619750  

If this is a compiler bug, could the project please document the list of supported Intel compilers?

---

## Comment 2

> Username: shubhdev  
> Created at: 2020-02-15 19:09:40 UTC  
> Url: https://github.com/boostorg/type_traits/issues/138#issuecomment-586631921  

This seems like a bug in ICC, particularly the expected SFINAE in presence of `sizeof(void)`doesn't take place which in this case means `boost::is_complete<void>` breaks.  
The below snippet reproduces what's going on. It fails to build on ICC 16, unless the fix is uncommented.  
```c++  
#include <boost/type_traits/is_complete.hpp>  
#include <boost/type_traits/integral_constant.hpp>  
  
#include <iostream>  
  
/*  
 From https://github.com/boostorg/type_traits/blob/develop/include/boost/type_traits/is_complete.hpp#L53  
*/  
template <unsigned N>  
struct ok_tag { double d; char c[N]; };  
  
template <class T>  
ok_tag<sizeof(T)> check_is_complete(int);  
template <class T>  
char check_is_complete(...);  
template <class T> struct is_complete  
      : public boost::integral_constant<bool, ::boost::is_function<typename boost::remove_reference<T>::type>::value || (sizeof(boost::detail::check_is_complete<T>(0)) != sizeof(char))> {};  
//  FIX  
// template <> struct is_complete<void> : public boost::false_type{};  
int main(int argc, char** argv)  
{  
    std::cout << is_complete<void>::value;  
   return 0;  
}  
```  
A fix (hack?) is to specialize the `is_complete` metafunction for `void`.
