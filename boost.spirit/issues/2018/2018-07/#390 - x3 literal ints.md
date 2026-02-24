# #390 - x3 literal ints [Closed]

> Username: dvirtz  
> Created at: 2018-07-16 08:17:20 UTC  
> Updated at: 2024-12-18 14:27:21 UTC  
> Closed at: 2024-12-18 14:27:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/390  

It seems that literal int parsers are not implemented yet.   
For example the code  
```c++  
std::string input = "5";  
assert(b::parse(input.begin(), input.end(), b::int_(5)));  
assert(b::parse(input.begin(), input.end(), b::lit(5)));  
```  
  
does not compile with   
```c++  
namespace b = boost::spirit::x3;  
```  
but does with   
```c++  
namespace b = boost::spirit::qi;  
```  
  
see https://wandbox.org/permlink/VFrnkOt9sTibHdvA  
  
is there an alternative or plans to support this?

---

## Comment 1

> Username: Kojoley  
> Created at: 2018-07-16 12:25:59 UTC  
> Url: https://github.com/boostorg/spirit/issues/390#issuecomment-405230726  

As a workaround you can use this https://wandbox.org/permlink/lRGojGA7HzM9ecMX  
  
```cpp  
#include <boost/spirit/home/x3.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
auto expect = [](auto value) {  
    return [=](auto& ctx) {  
        x3::_pass(ctx) = x3::_attr(ctx) == value;  
    };  
};  
  
template<typename T>  
auto lit_int(T value)  
{  
    return x3::int_parser<T>{}[expect(value)];  
}  
  
int main() {  
    std::string input = "5";  
    assert(parse(input.begin(), input.end(), x3::int_[expect(5)]));  
    assert(parse(input.begin(), input.end(), lit_int(5)));  
}  
```

---

## Comment 2

> Username: dvirtz  
> Created at: 2018-07-19 07:58:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/390#issuecomment-406189618  

Thanks.   
My solution is to convert it to a literal string parser using std::to_string:  
  
```c++  
int main() {  
    std::string input = "5";  
    assert(b::parse(input.begin(), input.end(), std::to_string(5)));  
}  
```

---

## Comment 3

> Username: djowel  
> Created at: 2019-05-07 04:34:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/390#issuecomment-489904393  

X3 is meant to be kept as simple as possible. I'm not sure we need literal ints.
