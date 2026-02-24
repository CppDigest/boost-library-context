# #340 - `BOOST_TEST(p1 == p2)` doesn't always test `p1 == p2` [Open]

> Username: pdimov  
> Created at: 2022-06-08 16:35:11 UTC  
> Updated at: 2022-06-08 21:17:15 UTC  
> Url: https://github.com/boostorg/test/issues/340  

A user of Boost.Test has complained to me that the behavior of `BOOST_TEST(p1 == p2)` when `p1` and `p2` are pointers to `char` is surprising as it silently reinterprets the C++ expression `p1 == p2` to mean something else, namely, `strcmp(p1, p2) == 0`. This is, in this user's opinion, a significant defect that justifies not using Boost.Test. (I don't necessarily agree.)  
  
I suspect that it's much too late now to change this behavior, as who knows how much existing code already relies on it, so I'm not sure what can be done to address this complaint.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-06-08 17:23:39 UTC  
> Url: https://github.com/boostorg/test/issues/340#issuecomment-1150192075  

Hi,  
If the user is already gone, then not much :)   
However this behaviour is documented [here](https://www.boost.org/doc/libs/1_79_0/libs/test/doc/html/boost_test/testing_tools/extended_comparison/strings.html) and the rationale is to have the comparison with `const char*` acting as a pointer to string.  
  
The solution is either `BOOST_TEST((p1 == p2))` or casting explicitly the pointers as `void *`.

---

## Comment 2

> Username: K-ballo  
> Created at: 2022-06-08 19:47:55 UTC  
> Url: https://github.com/boostorg/test/issues/340#issuecomment-1150335691  

User here.  
  
Are there any other cases in which `BOOST_TEST(expr)` will silently change the behavior of `expr` and test for something different instead?  
  
Could we get some configuration option to disable all those cases, such that `BOOST_TEST(expr)` tests `expr`?

---

## Comment 3

> Username: pdimov  
> Created at: 2022-06-08 20:10:10 UTC  
> Url: https://github.com/boostorg/test/issues/340#issuecomment-1150356121  

This behavior could be pretty surprising when one does e.g. `BOOST_TEST(x.begin() == x.end());` and the iterators happen to be pointers to char (as they are for `boost::string_view` for example, or can be, but aren't required to be, for `std::string_view`.)

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2022-06-08 21:17:15 UTC  
> Url: https://github.com/boostorg/test/issues/340#issuecomment-1150423894  

@K-ballo `BOOST_TEST(expr)` is always changing the behaviour of `expr` as it is interpreting the `expr` from left to right via operator overload. I can expand the section [here](https://www.boost.org/doc/libs/1_79_0/libs/test/doc/html/boost_test/testing_tools/boost_test_universal_macro.html#boost_test.testing_tools.boost_test_universal_macro.limitations_workaround) but that is not that helpful IMO.   
[Floating point comparison](https://www.boost.org/doc/libs/1_79_0/libs/test/doc/html/boost_test/testing_tools/extended_comparison/floating_point.html) is another example where the semantics of the comparison operators are changed.   
  
The easiest way to get around the limitations is to use the `BOOST_CHECK` and `BOOST_REQUIRE` macros instead. There is currently no such configuration and I believe there will not be.  
  
@pdimov this is a good example, and it will fail (or crash even) boost.test if `x.end()` goes past a string end `\0` (although there should be such a `\0` as the string_view is constructed from a valid string).   
  
I am open to suggestions to improve the flaws.
