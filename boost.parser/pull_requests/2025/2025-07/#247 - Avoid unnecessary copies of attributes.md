# #247 Avoid unnecessary copies of attributes. [Closed]

> Username: andreasbuhr  
> Created at: 2025-07-04 11:21:18 UTC  
> Updated at: 2025-07-13 09:28:54 UTC  
> Closed at: 2025-07-12 19:45:47 UTC  
> Url: https://github.com/boostorg/parser/pull/247  

Hi,  
  
sorry, I am new to boost.Parser, so I don't know whether this is wrong.  
I understand that every attribute is expected to be regular, so there needs to be a default-constructed / moved-from state.  
  
I thought it would be a nice property if attributes are never copied when they are in any other state than that. I.e. that they are never copied when carrying valuable content. It turned out that at the moment, they are copied while having content. For my example, this patch fixes the problem.   
  
I am not yet able to run unit tests, so I did not run them.  
  
Below I provide my reproducer, its output with this patch applied and its output without this patch applied.  
  
```c++  
#include <boost/parser/parser.hpp>  
#include <chrono>  
#include <string>  
  
  
namespace bp = boost::parser;  
  
class big_expensive_attribute {  
    public:  
    big_expensive_attribute() {  
        std::cout << "big_expensive_attribute default construction (ok)" << std::endl;  
    }  
    explicit big_expensive_attribute(std::string name) : name(name) {  
        std::cout << "big_expensive_attribute construction with name " << name << " (ok)" << std::endl;  
    }  
  
    big_expensive_attribute(const big_expensive_attribute &other) : name(other.name) {  
        std::cout << "big_expensive_attribute copy construction";  
        if (name) {  
            std::cout << " with payload " << *name << " (not ok, should be avoided)" << std::endl;  
        } else {  
            std::cout << " without payload (ok)" << std::endl;  
        }  
    }  
    big_expensive_attribute(big_expensive_attribute &&other)  noexcept : name(std::move(other.name)) {  
        std::cout << "big_expensive_attribute move construction (ok)" << std::endl;  
    }  
  
    big_expensive_attribute &operator=(const big_expensive_attribute &other) {  
        name = other.name;  
        std::cout << "big_expensive_attribute copy assignment" << std::endl;  
        if (name) {  
            std::cout << " with payload " << *name << " (not ok, should be avoided)" << std::endl;  
        } else {  
            std::cout << " without payload (ok)" << std::endl;  
        }  
        return *this;  
    }  
  
    big_expensive_attribute &operator=(big_expensive_attribute &&other)  noexcept {  
        name = std::move(other.name);  
        std::cout << "big_expensive_attribute move assignment (ok)" << std::endl;  
        return *this;  
    }  
  
    ~big_expensive_attribute() {  
        std::cout << "big_expensive_attribute destruction (ok)" << std::endl;  
    }  
  
    std::optional<std::string> name;  
};  
  
constexpr bp::rule<struct mystring, big_expensive_attribute> mystring = "mystring";  
constexpr auto mystring_def = bp::lexeme[+bp::char_('a','z')];;  
  
BOOST_PARSER_DEFINE_RULES(mystring)  
  
int main()  
{  
    const auto result = bp::parse(u8"hallo, welt", mystring % ',', bp::ws);  
    if (result) {  
        for (const auto &element : *result) {  
            std::cout << "Result: content: " << element.name.value_or("empty") << std::endl;  
        }  
    }  
}  
```  
  
Output with this patch applied:  
```  
big_expensive_attribute default construction (ok)  
big_expensive_attribute default construction (ok)  
big_expensive_attribute construction with name hallo (ok)  
big_expensive_attribute move assignment (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute move assignment (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute move construction (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute default construction (ok)  
big_expensive_attribute default construction (ok)  
big_expensive_attribute construction with name welt (ok)  
big_expensive_attribute move assignment (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute move assignment (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute move construction (ok)  
big_expensive_attribute move construction (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute destruction (ok)  
Result: content: hallo  
Result: content: welt  
big_expensive_attribute destruction (ok)  
big_expensive_attribute destruction (ok)  
```  
  
Output without this patch applied:  
```  
big_expensive_attribute default construction (ok)  
big_expensive_attribute default construction (ok)  
big_expensive_attribute construction with name hallo (ok)  
big_expensive_attribute move assignment (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute move assignment (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute move construction (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute default construction (ok)  
big_expensive_attribute default construction (ok)  
big_expensive_attribute construction with name welt (ok)  
big_expensive_attribute move assignment (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute move assignment (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute move construction (ok)  
big_expensive_attribute move construction (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute copy construction with payload hallo (not ok, should be avoided)  
big_expensive_attribute copy construction with payload welt (not ok, should be avoided)  
big_expensive_attribute destruction (ok)  
big_expensive_attribute destruction (ok)  
Result: content: hallo  
Result: content: welt  
big_expensive_attribute destruction (ok)  
big_expensive_attribute destruction (ok)  
```

---

## Comment 1

> Username: tzlaine  
> Created_at: 2025-07-12 19:45:47 UTC  
> Url: https://github.com/boostorg/parser/pull/247#issuecomment-3066003403  

Thanks for pointing this out.  I caught this and fixed it some time ago, but somehow it got reverted.  I may have done it on a branch....  
  
Anyway, I decided to make a slightly smaller change that avoids double-moving, so I'm not merging this change as-is.

---

## Comment 2

> Username: andreasbuhr  
> Created_at: 2025-07-13 08:33:43 UTC  
> Url: https://github.com/boostorg/parser/pull/247#issuecomment-3066721278  

cool, thx

---
