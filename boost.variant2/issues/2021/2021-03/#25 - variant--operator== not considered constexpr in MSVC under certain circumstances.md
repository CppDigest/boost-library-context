# #25 - variant::operator== not considered constexpr in MSVC under certain circumstances [Closed]

> Username: anarthal  
> Created at: 2021-03-07 20:10:57 UTC  
> Updated at: 2021-03-08 04:33:09 UTC  
> Closed at: 2021-03-08 04:33:09 UTC  
> Url: https://github.com/boostorg/variant2/issues/25  

Hi,  
  
I have been hitting a problem on MSVC when using `variant::operator==` in a `constexpr` context. Here is an example on Compiler Explorer of the behavior I'm seeing: https://godbolt.org/z/h4z9b8. Here is the code for reference:  
```  
#include <boost/variant2/variant.hpp>  
  
using boost::variant2::monostate;  
  
using myvariant = boost::variant2::variant<  
    monostate,  
    int  
>;  
  
int main()  
{  
    constexpr myvariant v0 {monostate{}};  
    constexpr myvariant v1 {monostate{}};  
    static_assert(v0 == v0, ""); // rejects this as not constexpr  
}  
```  
  
Works fine on clang and gcc. Interestingly, if I replace the `monostate` by a type like `float` or `string_view`, MSVC compiles it correctly.  
  
Use case: I am using `variant2` in Boost.Mysql to represent database values, and hit this when testing `operator==` for my value class.  
  
Thanks.

---

## Comment 1

> Username: pdimov  
> Created at: 2021-03-07 23:33:04 UTC  
> Url: https://github.com/boostorg/variant2/issues/25#issuecomment-792375028  

Thanks for the report.  
  
I believe this is a compiler bug (could be worth reporting it to MS.) There are two possible workarounds that I see: one, replace `struct monostate {};` with  
```  
struct monostate  
{  
    bool v{};  
};  
```  
Two, replace  
```  
constexpr bool operator==(monostate, monostate) noexcept { return true; }  
```  
with  
```  
constexpr bool operator==(monostate const&, monostate const&) noexcept { return true; }  
```  
I'll pick one of these and fix it, but not necessarily for 1.76 (sorry.)

---

## Comment 2

> Username: pdimov  
> Created at: 2021-03-08 04:33:09 UTC  
> Url: https://github.com/boostorg/variant2/issues/25#issuecomment-792454749  

Fixed. I chose option 2.
