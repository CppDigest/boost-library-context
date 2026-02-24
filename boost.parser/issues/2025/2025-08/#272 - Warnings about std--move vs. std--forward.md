# #272 - Warnings about std::move vs. std::forward [Closed]

> Username: jwwalker  
> Created at: 2025-08-27 23:23:49 UTC  
> Updated at: 2025-10-13 22:49:01 UTC  
> Closed at: 2025-10-13 22:49:01 UTC  
> Url: https://github.com/boostorg/parser/issues/272  

When I run the Clang static analyzer in Xcode 15.2 on an app using Parser, I get about 10 warnings like this:  
  
```  
warning: Forwarding reference passed to std::move(), which may unexpectedly cause lvalues to be moved; use std::forward() instead  
```  
  
One day I hope to understand `std::move` and `std::forward` well enough to know how much of a problem this is.

---

## Comment 1

> Username: andreasbuhr  
> Created at: 2025-08-28 14:30:55 UTC  
> Url: https://github.com/boostorg/parser/issues/272#issuecomment-3233741882  

Can you maybe provide the information in which file and on which line these warnings occur?

---

## Comment 2

> Username: jwwalker  
> Created at: 2025-08-28 16:02:28 UTC  
> Updated at: 2025-08-28 17:29:14 UTC  
> Url: https://github.com/boostorg/parser/issues/272#issuecomment-3234087860  

In Boost 1_88_0, I get that static analysis warning in these locations:  
  
`/boost/parser/detail/text/detail/all_t.hpp` line 129  
  
`/boost/parser/parser.hpp` lines 1362, 1954, 1956, 1990, 2000, 2017, 2032, 2101, 2109, and 2123.  
  
In Boost 1_89_0, I can't do a static analysis, because I get a compile error at `/boost/parser/parser.hpp` line 583, `error: assigning to 'nope_or_pointer_t<double>' from incompatible type 'const nope_or_pointer_t<boost::parser::detail::nope>'`. See #274.  
  
(When I first posted this issue, I was unknowingly still using 1_88_0 due to a caching situation.)

---

## Comment 3

> Username: jwwalker  
> Created at: 2025-09-02 22:16:26 UTC  
> Url: https://github.com/boostorg/parser/issues/272#issuecomment-3246963177  

Now I have my project working with Boost 1.89.0.  The warning locations are:  
  
`/boost/parser/detail/text/detail/all_t.hpp` line 129, in a template function `all_impl::operator()(R && r) const`  
  
The rest are in `/boost/parser/parser.hpp` at these locations:  
  
line 1362, template function `void append(Container & c, T && x, bool gen_attrs)`  
  
line 1954 and 1956, template function `make_from_tuple_impl(Tuple && tup, std::integer_sequence<int, Is...>)`  
  
lines 1990, 2000, 2017, template function `move_back_impl(Container & c, U && x)`  
  
line 2032, template function `move_back(Container & c, T && x, bool gen_attrs)`  
  
line 2101, 2109, 2123 template function `void assign(T & t, U && u)`  
  
As I understand it, in order to figure out whether this is a theoretical problem or a real problem, one would have to examine all the places where these functions are called, and see whether an lvalue reference is ever being passed as the indicated parameter.  But it shouldn't do any harm to replace `std::move` with `std::forward` as suggested.

---

## Comment 4

> Username: tzlaine  
> Created at: 2025-10-13 22:49:01 UTC  
> Url: https://github.com/boostorg/parser/issues/272#issuecomment-3399310152  

Addressed in 159472ac6ea8243726f937a59ab3f3fa86eff948.
