# #343 - hana::equal does not support reference wrappers [Closed]

> Username: ricejasonf  
> Created at: 2017-05-04 05:49:30 UTC  
> Updated at: 2017-05-06 04:11:02 UTC  
> Closed at: 2017-05-05 22:17:42 UTC  
> Url: https://github.com/boostorg/hana/issues/343  

I stumbled upon this trying to write a test comparing two tuples one of which contained a reference wrapped value and the other contained a raw value.  
  
Consider the following:  
```cpp  
#include <boost/hana.hpp>  
#include <functional>  
#include <string>  
  
namespace hana = boost::hana;  
  
int main() {  
  std::string foo("Hello, world!");  
  
  hana::equal(std::cref(foo), foo);  
}  
```  
  
It fails to compile with the following output:  
```  
/usr/local/include/boost/hana/equal.hpp:60:13: error: static_assert failed "No default implementation of  
      hana::equal is provided for related types that can't be safely embedded into a common type, because  
      those are most likely programming errors. If this is really what you want, you can manually convert  
      both objects to a common Comparable type before performing the comparison."  
            static_assert(!hana::is_convertible<T_, U>::value &&  
            ^             ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/hana/equal.hpp:51:23: note: in instantiation of function template specialization  
      'boost::hana::equal_impl<std::reference_wrapper<const std::basic_string<char> >,  
      std::basic_string<char>, boost::hana::when<true> >::apply<std::reference_wrapper<const  
      std::basic_string<char> >, std::basic_string<char> >' requested here  
        return Equal::apply(static_cast<X&&>(x), static_cast<Y&&>(y));  
                      ^  
ref_equal.cpp:10:14: note: in instantiation of function template specialization  
      'boost::hana::equal_t::operator()<std::reference_wrapper<const std::basic_string<char> >,  
      std::basic_string<char> &>' requested here  
  hana::equal(std::cref(foo), foo);  
```  
  
Does it make sense to provide equality for reference wrapped values with raw values?

---

## Comment 1

> Username: ricejasonf  
> Created at: 2017-05-05 22:17:42 UTC  
> Url: https://github.com/boostorg/hana/issues/343#issuecomment-299588509  

After running into issues with other functions having the same problem (including my own), I don't know that this is worth looking at. Functions would have to magically unwrap the refs and `hana::tag_of` would have to support it as well.  
  
I'll just close this. :sweat_smile:

---

## Comment 2

> Username: ldionne  
> Created at: 2017-05-06 04:11:02 UTC  
> Url: https://github.com/boostorg/hana/issues/343#issuecomment-299614252  

Sorry for the lack of reply. Yeah, in general reference wrappers are difficult to support properly, and often times that's not actually what you want.
