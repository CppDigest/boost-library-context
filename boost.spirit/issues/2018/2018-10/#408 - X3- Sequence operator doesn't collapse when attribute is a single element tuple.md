# #408 - X3: Sequence operator doesn't collapse when attribute is a single element tuple [Closed]

> Username: syyyr  
> Created at: 2018-10-15 13:51:27 UTC  
> Updated at: 2021-10-19 20:16:48 UTC  
> Closed at: 2018-10-24 15:56:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/408  

Hello, I'm trying to make a rule, that parses integers separated by a slash, and the last one is separated by an asterisk:  
```  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
namespace x3 = boost::spirit::x3;  
  
struct ints_ {  
    std::vector<int> m_vector;  
};  
BOOST_FUSION_ADAPT_STRUCT(ints_, m_vector)  
  
struct ints_class;  
x3::rule<ints_class, ints_> const ints = "ints";  
auto const ints_def = x3::int_ % '/' >> '*' >> x3::int_;  
BOOST_SPIRIT_DEFINE(ints)  
  
int main(int argc, char* argv[])  
{  
    ints_ foo;  
    std::string to_parse = "3432/432/4*4343";  
    auto to_parse_iter = to_parse.begin();  
    x3::parse(to_parse_iter, to_parse.end(), ints, foo);  
}  
```  
Unfortunately, the code doesn't compile with this error:  
```  
/usr/include/boost/spirit/home/x3/operator/detail/sequence.hpp:150:25: error: static assertion failed: Size of the passed attribute is less than expected.  
             actual_size >= expected_size  
```  
 According to the Compound Attribute Rules section in the documentation, the attribute of the ```ints``` rule should be ```vector<int>```:  
```  
a: A, b: B --> (a % b): vector<A>  
a: vector<A>, b: A --> (a >> b): vector<A>  
```  
If I add another ```int``` field to the struct, it successfully compiles, but I would like to have all the integers in the same vector. Is there a solution to this? Am I not doing something right?

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-10-15 14:23:10 UTC  
> Updated at: 2021-10-19 20:14:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/408#issuecomment-429872835  

You want too much guessing from Spirit. It will not do collapsing because you want to parse sequence into tuple attribute (and size of the parser sequences do not match the size of the tuple attribute). To fix your code you need to add an intermediate rule which will do collapsing (and which result should be of `std::vector` type). But as a general rule I will suggest you to not use single element tuples (you can simply replace `ints_` with `std::vector<int>` in your case, or with `struct ints_ : std::vector<int> { };` if you need a "strong typedef").  
  
> If I add another `int` field to the struct, it successfully compiles  
  
That extends your tuple type from `tuple<vector<int>>` to `tuple<vector<int>, int>` which is perfectly compatible with `x3::int_ % '/' >> '*' >> x3::int_` parser.  
  
It is not a bug in Spirit but a missing feature, and I do not think it is worth the complexity (refs https://github.com/boostorg/spirit/pull/178#issuecomment-198802189 and https://github.com/boostorg/spirit/issues/463).

---

## Comment 2

> Username: syyyr  
> Created at: 2018-10-24 15:44:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/408#issuecomment-432712485  

Okay, so the root problem lies in that I use single element structures?

---

## Comment 3

> Username: Kojoley  
> Created at: 2018-10-24 15:56:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/408#issuecomment-432717410  

> Okay, so the root problem lies in that I use single element structures?  
  
Yes, quickfix is to replace  
  
```cpp  
struct ints_ {  
    std::vector<int> m_vector;  
};  
BOOST_FUSION_ADAPT_STRUCT(ints_, m_vector)  
```  
  
with  
  
```cpp  
struct ints_ : std::vector<int> {};  
```  
  
or even  
  
```cpp  
typedef std::vector<int> ints_;  
```

---

## Comment 4

> Username: syyyr  
> Created at: 2018-10-24 16:00:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/408#issuecomment-432719169  

Okay, thank you.
