# #748 - no_case is a type in some make_primitives specializations, and a bool in others. [Open]

> Username: BengtGustafsson  
> Created at: 2022-12-27 13:44:03 UTC  
> Updated at: 2025-05-09 23:24:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/748  

`make_primitives<...>::no_case` is a type in some make_primitives specializations, and a bool in others. This seems inconsistent, and I wonder if this is a bug:  
  
boost/spirit/home/qi/string/lit.hpp:263:77: boost::spirit::qi::make_primitive::no_case is a type   
boost/spirit/home/qi/char/char_class.hpp:98:27: boost::spirit::qi::make_primitive::no_case is a value  
boost/spirit/home/qi/string/symbols.hpp:395:77: boost::spirit::qi::make_primitive::no_case is a type   
  
The reason I'm asking is that I wrote a proposal [P2669 ](https://www.open-std.org/jtc1/sc22/wg21/docs/papers/2022/p2669r0.pdf) to disallow such differences. When I presented it at the C++ standardization meeting some were worried that a lot of code would break, and that those breakages would not be bugs. To check the situation I made a Clang plugin which detects such differences. I then compiled quite a few repos (using vcpkg) and in Boost I encountered this only case where code would break if P2669 was standardized.  
  
So I would be very interested in if this is indeed a bug or if you have good usage for the different kinds of the no_code name in different specializations. In the latter case it would be very interesting to get a statement regarding the complexities involved in fixing this if P2669 was standardized.

---

## Comment 1

> Username: djowel  
> Created at: 2022-12-27 14:02:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/748#issuecomment-1365923143  

I think it is a bug.
