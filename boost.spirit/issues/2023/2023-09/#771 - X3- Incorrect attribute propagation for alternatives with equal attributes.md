# #771 - X3: Incorrect attribute propagation for alternatives with equal attributes [Closed]

> Username: Krzmbrzl  
> Created at: 2023-09-22 12:39:11 UTC  
> Updated at: 2025-05-10 02:03:39 UTC  
> Closed at: 2023-09-23 09:07:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/771  

The [docs](https://www.boost.org/doc/libs/1_83_0/libs/spirit/doc/x3/html/spirit_x3/quick_reference/compound_attribute_rules.html) state  
  
> a: A, b: A --> (a | b): A  
  
meaning that an alternative between two rules/parsers that have the same attribute type will have an attribute that is the same as that common type (instead of a variant of the individual types).  
  
However, this is not the case. Alternatives such as the above still result in an attribute of type `variant< A, A >`.  
  
While (I think?) automatic propagation rules can correctly propagate this to an attribute of type `A`, this is a problem/inconvenience for e.g. semantic actions as they get the immediate attribute type and thus have to work with the variant.  
  
MWE ([Godbolt](https://godbolt.org/z/cGbdMovsx)):  
```cpp  
#include <boost/spirit/home/x3.hpp>  
  
#include <type_traits>  
#include <string>  
  
namespace x3 = boost::spirit::x3;  
  
int main() {  
    auto validator = [](auto &ctx) {  
        static_assert(std::is_same_v<std::remove_cvref_t<decltype(x3::_attr(ctx))>, int>);  
    };  
    auto parser = (x3::int_ | x3::int_)[validator];  
  
    std::string input;  
    x3::phrase_parse(input.begin(), input.end(), parser, x3::space);  
}  
```

---

## Comment 1

> Username: cppljevans  
> Created at: 2023-09-22 18:30:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/771#issuecomment-1731877480  

think this issue has already been raised in [issue 722](https://github.com/boostorg/spirit/issues/722).

---

## Comment 2

> Username: Krzmbrzl  
> Created at: 2023-09-23 09:07:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/771#issuecomment-1732261009  

Yes indeed.
