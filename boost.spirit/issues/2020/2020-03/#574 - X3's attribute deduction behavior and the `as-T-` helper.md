# #574 - X3's attribute deduction behavior and the `as<T>` helper [Closed]

> Username: saki7  
> Created at: 2020-03-05 00:38:49 UTC  
> Updated at: 2020-03-06 16:45:18 UTC  
> Closed at: 2020-03-06 16:23:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/574  

X3 sometimes fails to deduce the appropriate attribute types for certain complex (nested) rules.  
  
Take a look at these situations:  
  
- [c++ - spirit x3 cannot propagate attributes of type optional<vector> - Stack Overflow](https://stackoverflow.com/questions/43089395/spirit-x3-cannot-propagate-attributes-of-type-optionalvector/43093437#43093437)  
- [c++ - Understanding the List Operator (%) in Boost.Spirit - Stack Overflow](https://stackoverflow.com/questions/33816662/understanding-the-list-operator-in-boost-spirit/33817135#33817135)  
- [X3 lacks 'lazy' parser · Issue boostorg/spirit#838 · boostorg/spirit](https://github.com/boostorg/spirit/issues/838)  
  
Using the helper function `as<T>` like the one @sehe suggests solves the problem. I personally have faced similar situations in more than three applications which use X3, and I'm writing my own `as<T>` function everytime. Is there any plans to support this helper officially as `boost::spirit::x3::as<T>` ?

---

## Comment 1

> Username: Kojoley  
> Created at: 2020-03-06 01:54:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/574#issuecomment-595557868  

An example from the first list does compile since 1.67; the second link does not contain any bug, it might be a future request, but it is not an easy thing to implement; the third link I do not get.

---

## Comment 2

> Username: saki7  
> Created at: 2020-03-06 04:47:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/574#issuecomment-595598158  

Apologies for the link I posted at first. Those codes are indeed old and unreproducible.  
  
The problem still persists. I've just tested below code on Boost 1.72.0, GCC HEAD and Clang HEAD.  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
  
struct Int  
{  
    int value;  
};  
  
BOOST_FUSION_ADAPT_STRUCT(Int, value)  
  
int main()  
{  
    std::string const input = "42";  
      
    using Attr = boost::variant<Int, std::string>;  
    Attr attr;  
      
    // compile error:  
    auto const rule = x3::int_ | +x3::char_;  
      
    // OK:  
    //auto const rule = (x3::rule<struct _, Int> {} = x3::int_) | +x3::char_;  
      
    if (x3::parse(std::begin(input), std::end(input), rule, attr)) {  
        std::cout << "matched" << std::endl;  
        std::cout << "value: " << boost::get<Int>(attr).value << std::endl;  
  
    } else {  
        std::cout << "no match" << std::endl;  
    }  
}  
```

---

## Comment 3

> Username: Kojoley  
> Created at: 2020-03-06 13:40:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/574#issuecomment-595771984  

Single element tuples are not fully supported, see boostorg/spirit#463.

---

## Comment 4

> Username: saki7  
> Created at: 2020-03-06 16:23:53 UTC  
> Updated at: 2020-03-06 16:24:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/574#issuecomment-595845957  

I see. I find that many parts of my application code can be resolved by properly handling single element tuples. I can't come up with more appropriate example regarding `as<T>` so I'm closing this issue. Thanks for your comments :)

---

## Comment 5

> Username: Kojoley  
> Created at: 2020-03-06 16:45:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/574#issuecomment-595855896  

The example above needs an implicit conversion from `int` to `Int` to compile, but I do not think it is a bug.
