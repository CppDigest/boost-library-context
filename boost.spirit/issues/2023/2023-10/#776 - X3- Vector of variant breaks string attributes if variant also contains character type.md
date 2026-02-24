# #776 - X3: Vector of variant breaks string attributes if variant also contains character type [Open]

> Username: sehe  
> Created at: 2023-10-13 19:11:31 UTC  
> Updated at: 2025-05-09 22:47:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/776  

Observed from [here](https://stackoverflow.com/a/77288311/85371), reproducer:  
  
[Live On Coliru](https://coliru.stacked-crooked.com/a/eedd736998265c60):  
  
```c++  
#include <boost/spirit/home/x3.hpp>  
#include <iomanip>  
#include <iostream>  
  
namespace x3 = boost::spirit::x3;  
constexpr static inline std::string_view sample = R"("asdf" "xxx" "yyy")";  
  
template <typename CharType> size_t test() {  
    static auto const qstr                      //  
        = x3::rule<class _, std::string>{"str"} //  
        = x3::lexeme['"' >> *~x3::char_('"') >> '"'];  
  
    using Var = boost::variant<std::string, CharType>;  
  
    std::vector<Var> data;  
    phrase_parse(sample.begin(), sample.end(), *qstr, x3::space, data);  
    return data.size();  
}  
  
#define TEST(CharType) \  
    std::cout << std::setw(13) << #CharType << " -> " << test<CharType>() << std::endl;  
  
int main() {  
    TEST(uint8_t);  
    TEST(int8_t);  
    TEST(signed char);  
    TEST(unsigned char);  
  
    TEST(char);  
}  
```  
  
Prints  
  
```  
      uint8_t -> 3  
       int8_t -> 3  
  signed char -> 3  
unsigned char -> 3  
         char -> 10  
```  
  
As we can see, having `char` inside the variant breaks string parsing, instead treating the vector as the "string container", filling it with the individual characters.  
  
This breaking change happened in c60d93f which was "**bug fix for x3 where container attribute is substitute for the container value type**" (indeed the output was consistent with boost 1.58.0)  
  
I think the exception introduced there should not apply here as the `rule` declares an attribute of `std::string` explicitly, and as such should not expect `vector<variant<..., char>>` to be a match.

---

## Comment 1

> Username: saki7  
> Created at: 2025-05-09 22:47:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/776#issuecomment-2868005730  

This could be either an intended behavior or a misfeature. I'm not sure which is correct.   
I'm also not sure if this issue persists on current codebase (as of 2025.)
