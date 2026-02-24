# #167 - boost::core::string_view gives ambiguous error [Open]

> Username: Spongman  
> Created at: 2024-02-28 18:15:19 UTC  
> Updated at: 2024-02-29 03:41:14 UTC  
> Url: https://github.com/boostorg/core/issues/167  

(1.84 gcc 13.2)  
  
upgrading boost from 1.75 to 1.84 breaks existing code that boils down to:  
  
  
````c++  
#include <boost/core/detail/string_view.hpp>  
  
struct Foo  
{  
	Foo(std::string_view f) {}  
	Foo(const std::string& f) {}  
};  
  
int main() {  
	Foo{std::string_view()};         // works  
	Foo{boost::core::string_view()}; // fails  
  
	//Foo{boost::beast::http::fields{}.find("")->value()}; // same failure, works in 1.75  
}  
````  
https://godbolt.org/z/8TKfP6Pj8  
  
  
```  
<source>: In function 'int main()':  
<source>:11:39: error: call of overloaded 'Foo(<brace-enclosed initializer list>)' is ambiguous  
   11 |         Foo{boost::core::string_view()};  
      |                                       ^  
<source>:6:9: note: candidate: 'Foo::Foo(const std::string&)'  
    6 |         Foo(const std::string& f) {}  
      |         ^~~  
<source>:5:9: note: candidate: 'Foo::Foo(std::string_view)'  
    5 |         Foo(std::string_view f) {}  
      |         ^~~  
Compiler returned: 1  
```  
  
  
  
it _used_ to work with `BOOST_BEAST_USE_STD_STRING_VIEW`. how do I get it to use `std::string_view` like before?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-02-28 19:00:50 UTC  
> Url: https://github.com/boostorg/core/issues/167#issuecomment-1969653052  

Support for `BOOST_BEAST_USE_STD_STRING_VIEW` was removed 2 years ago and I don't think that the maintainers of Beast will be willing to reinstate it, so I don't think you can get Beast to use `std::string_view` like before.  
  
`core::string_view` does convert to both `std::string` and `std::string_view`, so the ambiguity is legitimate. It might be possible to fix; I'll have to think about it.  
  
The easiest fix from your side (which may not be applicable) would be to simply remove the constructor taking `std::string const&` and rely on the `std::string_view` constructor for `std::string` arguments. I don't know if that'll work for you, though; maybe you have legitimate reasons for having the `std::string const&` constructor (e.g. guaranteed null termination).

---

## Comment 2

> Username: Spongman  
> Created at: 2024-02-28 20:21:56 UTC  
> Url: https://github.com/boostorg/core/issues/167#issuecomment-1969847609  

no, that doesn't work. `Foo` is in a 3rd-party library that I can't change, and that overload supports implicit conversion, basically:  
```  
	[](const Foo&){}(std::string{});  
```  
  
super confusing to me why std is being deprecated.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-02-29 03:41:13 UTC  
> Url: https://github.com/boostorg/core/issues/167#issuecomment-1970341357  

What's the actual `Foo`?
