# #289 Include at_key.hpp in map to fix issue with generic map access [Closed]

> Username: jacquelinekay  
> Created at: 2016-06-14 06:05:03 UTC  
> Updated at: 2016-06-19 17:02:20 UTC  
> Closed at: 2016-06-19 17:02:20 UTC  
> Url: https://github.com/boostorg/hana/pull/289  

This is a small gripe, but I like to include the minimal set of headers in my project wherever possible. I ran into an issue with this in an example accessing a `hana::map` where the key is `type_c<T>` and T is a template parameter.  
  
On develop with Clang 3.8, using the `examples` CMake target, the following code doesn't compile:  
  
```  
#include <boost/hana/map.hpp>  
#include <iostream>  
  
namespace hana = boost::hana;  
  
constexpr auto max_value_map = hana::make_map(  
  hana::make_pair(hana::type_c<unsigned char>,  255),  
  hana::make_pair(hana::type_c<unsigned short>, 65535)  
);  
  
template<typename T>  
void print_value() {  
  std::cout << "maximum value is: " << max_value_map[hana::type_c<T>] << std::endl;  
}  
  
int main() {  
  print_value<unsigned char>();  
}  
```  
  
The error message originates at:  
  
```  
hana/include/boost/hana/detail/operators/searchable.hpp:34:20: error:   
      function 'operator()<const  
      boost::hana::map<boost::hana::detail::hash_table<boost::hana::detail::bucket<unsigned char,  
      0>, boost::hana::detail::bucket<unsigned short, 1>, boost::hana::detail::bucket<unsigned int,  
      2>, boost::hana::detail::bucket<unsigned long, 3> >,  
      boost::hana::basic_tuple<boost::hana::pair<boost::hana::type_impl<unsigned char>::_, int>,  
      boost::hana::pair<boost::hana::type_impl<unsigned short>::_, int>,  
      boost::hana::pair<boost::hana::type_impl<unsigned int>::_, unsigned int>,  
      boost::hana::pair<boost::hana::type_impl<unsigned long>::_, unsigned long> > > &,  
      boost::hana::type_impl<unsigned char>::_>' with deduced return type cannot be used before it  
      is defined  
            return hana::at_key(static_cast<Derived const&>(*this),  
```  
  
It did compile with `boost/hana.hpp`, but I thought we could do a bit better. Including `boost/hana/at_key.hpp` fixed my example. I thought it would be more intuitive if `boost/hana/map.hpp` pulled in that header.

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2016-06-14 07:08:36 UTC  
> Updated_at: 2016-06-14 07:17:16 UTC  
> Url: https://github.com/boostorg/hana/pull/289#issuecomment-225797354  

The fix should be in `detail/operators/searchable.hpp`. Since it is actually calling the function there, it should include `at_key.hpp` and not `fwd/at_key.hpp`.  
  
It appears **all** of the operators are including the `fwd/___.hpp` files so this problem is not limited to `map` and `at_key`.

---

## Comment 2

> Username: ldionne  
> Created_at: 2016-06-14 15:07:25 UTC  
> Url: https://github.com/boostorg/hana/pull/289#issuecomment-225912203  

Generally speaking, your example would need the following to compile:  
  
``` c++  
#include <boost/hana/at_key.hpp>  
#include <boost/hana/map.hpp>  
#include <boost/hana/pair.hpp>  
#include <boost/hana/type.hpp>  
  
#include <iostream>  
  
namespace hana = boost::hana;  
  
constexpr auto max_value_map = hana::make_map(  
  hana::make_pair(hana::type_c<unsigned char>,  255),  
  hana::make_pair(hana::type_c<unsigned short>, 65535)  
);  
  
template<typename T>  
void print_value() {  
  std::cout << "maximum value is: " << max_value_map[hana::type_c<T>] << std::endl;  
}  
  
int main() {  
  print_value<unsigned char>();  
}  
```  
  
I think the `type.hpp` and `pair.hpp` headers are easy to justify. Now, the `at_key` is required because `operator[]` is equivalent to `at_key`, and Hana requires that you include the headers of the operations you use to work. The subtle part is realizing that `operator[]`, a member function of `map`, in fact requires an additional include. I agree that this is tricky.  
  
However, if we change this, should we also make it so that `operator==` does not require including `boost/hana/equal.hpp`? This certainly causes a problem (and it applies to all operators), because many use cases do not require any sort of comparison and would rather not include these headers indirectly.  
  
So the tradeoff here is ease-of-use vs minimality of headers. The current approach favors minimality of includes, and provides the master header (`boost/hana.hpp`) for quick prototyping where you want ease-of-use. I think that consistency dictates that whatever we do for `operator[]` is also done for other operators, if we do anything. Thoughts/reactions?

---

## Comment 3

> Username: ldionne  
> Created_at: 2016-06-14 15:08:54 UTC  
> Url: https://github.com/boostorg/hana/pull/289#issuecomment-225912688  

@ricejasonf Btw, including the `fwd` header in `operators/searchable.hpp` instead of the full header was on purpose. Otherwise, any structure wanting to have the operators would have to pull in the implementation of many other (and sometimes unrelated) algorithms.

---

## Comment 4

> Username: jacquelinekay  
> Created_at: 2016-06-15 05:07:55 UTC  
> Url: https://github.com/boostorg/hana/pull/289#issuecomment-226089080  

Yes, it makes sense and I'm fine to close this without merging. I found it strange initially because in a "typical" OO library I would expect operator[] to be provided as a member function of a class in the same header or an included header. But since `at_key` is actually a free function template, the `map class definition can still be complete if the definition is provided in a separate header with no dependency. And it's actually more consistent with the goal of minimal header includes to do so. Thanks for the explanation.

---

## Comment 5

> Username: ldionne  
> Created_at: 2016-06-19 17:02:20 UTC  
> Url: https://github.com/boostorg/hana/pull/289#issuecomment-227008156  

Ok, closing then. FWIW, it **is** a valid concern whether one should optimize for ease of use vs header minimality. The library _used to_ optimize for ease of use, and including `hana/map.hpp` would have included all the algorithms supported by `hana::map`. However, this was changed a while ago because I thought header minimality was more important, and ease of use could be achieved through the master header.

---
