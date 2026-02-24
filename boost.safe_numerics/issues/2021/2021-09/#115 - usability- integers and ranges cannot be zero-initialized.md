# #115 - usability: integers and ranges cannot be zero-initialized [Closed]

> Username: hgkjshegfskef  
> Created at: 2021-09-10 12:31:45 UTC  
> Updated at: 2021-09-10 15:36:45 UTC  
> Closed at: 2021-09-10 15:36:45 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/115  

Here is a slight usability inconvenience that I have discovered: while literals can be zero-initialized, integers and ranges can only be value-initialized. See the following example (also live at https://godbolt.org/z/b76f4cq9h ):  
  
```cpp  
#include <boost/safe_numerics/automatic.hpp>  
#include <boost/safe_numerics/safe_integer.hpp>  
#include <boost/safe_numerics/safe_integer_literal.hpp>  
#include <boost/safe_numerics/safe_integer_range.hpp>  
#include <cstdint>  
  
template <std::uintmax_t u>  
using safe_uliteral_auto_trap = boost::safe_numerics::safe_unsigned_literal<  
    u, boost::safe_numerics::automatic,  
    boost::safe_numerics::loose_trap_policy>;  
  
template <std::uintmax_t u_begin, std::uintmax_t u_end>  
using safe_urange_auto_trap = boost::safe_numerics::safe_unsigned_range<  
    u_begin, u_end, boost::safe_numerics::automatic,  
    boost::safe_numerics::loose_trap_policy>;  
  
template <typename Type>  
using safe_auto_trap =  
    boost::safe_numerics::safe<Type, boost::safe_numerics::automatic,  
                               boost::safe_numerics::loose_trap_policy>;  
  
// Literals can be zero-initialized:  
constexpr safe_uliteral_auto_trap<0> min{};  
constexpr safe_uliteral_auto_trap<1> max{};  
  
// Ranges cannot be zero-initialized:  
// constexpr safe_urange_auto_trap<min, max> range{};  
// They can only be value-initialized:  
constexpr safe_urange_auto_trap<min, max> range{min};  
  
// Integers cannot be zero-initialized:  
// constexpr safe_auto_trap<int> i{};  
// They can only be value-initialized:  
constexpr safe_auto_trap<int> i{min};  
```  
  
In `constexpr` context it is required to have everything initialized, and sometimes it doesn't even matter to what value (example: using a ranged integer to use the lower and upper bounds, in order to not manually create yet another custom aggregate type called Range).  
  
Considering we can't simply write:  
```cpp  
constexpr safe_urange_auto_trap<min, max> range{0};  
```  
in trap policy (we can in exception), it is required to write something like this:  
```cpp  
constexpr safe_urange_auto_trap<min, max> range{safe_uliteral_auto_trap<0>{}};  
// or:  
constexpr safe_uliteral_auto_trap<0> min{};  
constexpr safe_urange_auto_trap<min, max> range{min};  
```  
  
which is quite verbose. Perhaps for ranges it can be questionable, what the zero value is for each particular range (my opinion: if `0` is in range, it is `0`, if not, it is `std::numeric_limits<range>::min()`), but for integers `0` would probably fit the shoe in all cases, regardless of type.  
  
What do you think?

---

## Comment 1

> Username: robertramey  
> Created at: 2021-09-10 14:00:15 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/115#issuecomment-916926834  

a) Rather than "zero initialized" I think what we want to say is "default initialized".   
b) literals are initialized by there design so there is no problem here  
c) for trap policy, we cannot write:  
`constexpr safe_urange_auto_trap<min, max> range = 0;`  
but must write the following.  
`constexpr safe_urange_auto_trap<min, max> range =  min`  
  
This is indeed an issue.  Ideally both should work the same.  But 0 is a "runtime" value so it can' t trap. C++20 improves constexpr so 0 will be a "compile time" value so one should be able to use the first.  This requires some adjustments to the code which turn out to be non-trivial.  I'm working on it now.  
  
I'm impressed that you've been able to figure out these arcane points.

---

## Comment 2

> Username: robertramey  
> Created at: 2021-09-10 14:01:38 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/115#issuecomment-916927810  

actually its not "zero initialized" nor "default initialized" its really "constexpr intialized".  But of course that would also be confusing.  oh well.

---

## Comment 3

> Username: hgkjshegfskef  
> Created at: 2021-09-10 15:36:45 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/115#issuecomment-916997718  

Regarding `0` being 'runtime' value: I see. And good to know you are working on C++20 features.  
  
And sorry for the confusion in terminology. I forgot that my example is in static context, and how initialization of static variables works. Indeed, what happens here is to my understanding called constant initialization. There is also zero initialization in static context, which is used when compiler cannot evaluate the expression at compile time. Here is a good article about initialization in static context: https://pabloariasal.github.io/2020/01/02/static-variable-initialization/  
  
Not to be confused with zero initialization in 'normal' (or whatever it is called) context, e.g. in function scope, which can be written as `foo bar{};`. This is part of a bigger kind of initialization -- value initialization, which would look like `foo bar{baz};`. Both will make sure `bar` is in some valid state. Default initialization looks like `foo bar;` and will leave `bar` with uninitialized value inside, and reading through it will cause UB, unless `foo` has default member initializers, or something. So, when I said 'zero-initialized' or 'value-initialized' I meant these two. And if I indeed do what I meant in words, there is no inconvenience, because all 3 kinds of initializations will compile just fine:  
```cpp  
int main() {  
 safe_urange_auto_trap<min, max> range1;  
 safe_urange_auto_trap<min, max> range2{};  
 safe_urange_auto_trap<min, max> range3{min};  
}  
```
