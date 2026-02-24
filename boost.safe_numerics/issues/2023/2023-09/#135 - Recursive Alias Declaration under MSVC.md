# #135 - Recursive Alias Declaration under MSVC [Open]

> Username: christianbrugger  
> Created at: 2023-09-19 10:43:18 UTC  
> Updated at: 2023-10-07 05:57:23 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/135  

To my understanding `safe<>` should work in any template as integer safety is orthogonal.  
  
I am unable to use `safe<int>` in `std::span` in MSVC:  
  
```c++  
#include <span>  
#include <vector>  
#include <boost/safe_numerics/safe_integer.hpp>  
  
using Int = boost::safe_numerics::safe<int>;  
  
auto foo_safe(std::span<const Int> numbers) -> int {  
    return int {numbers[0]};  
}  
  
int main() {  
    const auto numbers = std::vector<Int> {  
        Int {1}, Int {2}, Int {3}  
    };  
      
    return foo_safe(numbers);  
}  
  
```  
  
This leads to a **recursive alias declaration** error:  
```  
error C2968:   
'legal_overload<boost::safe_numerics::addition_operator,std::_Vector_const_iterator<std::_Vector_val  
<std::_Simple_types<boost::safe_numerics::safe_base<int,-2147483648,2147483647,boost::  
safe_numerics::native,boost::safe_numerics::exception_policy<  
boost::safe_numerics::throw_exception,boost::safe_numerics::throw_exception,boost::  
safe_numerics::throw_exception,boost::safe_numerics::ignore_exception> > > > >,__int64>':   
recursive alias declaration  
```  
  
[See on godbolt.](https://godbolt.org/z/831ThMsP1)

---

## Comment 1

> Username: christianbrugger  
> Created at: 2023-10-06 14:50:44 UTC  
> Updated at: 2023-10-06 15:11:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/135#issuecomment-1750812849  

Slowly integrating safe_numerics in my project I noticed, I get the same error with `std::chrono::duration`:  
  
```c++  
#include <boost/safe_numerics/safe_integer.hpp>  
#include <chrono>  
  
using Int = boost::safe_numerics::safe<int>;  
using Duration = std::chrono::duration<Int, std::nano>;  
  
int main()  
{  
    auto v1 = Duration {Int{1}};  
    auto v2 = Duration {Int{2}};  
  
    v1 += v2;           // error C2968: recursive alias declaration  
    v1 = v1 * Int {2};  // error C2968: recursive alias declaration  
    // v1 = v1 + v2;    // this works  
  
    return v1.count();  
}  
```  
[Godbolt](https://godbolt.org/z/nezc4aTGc)  
  
```  
C:\data\libraries\installed\x64-windows\include\boost\safe_numerics\safe_base_operations.hpp(371): error C2968:   
'legal_overload<boost::safe_numerics::addition_operator,std::chrono::duration<boost::safe_numerics::safe_base  
<int,-2147483648,2147483647,boost::safe_numerics::native,boost::safe_numerics::exception_policy  
<boost::safe_numerics::throw_exception,boost::safe_numerics::throw_exception,boost::safe_numerics::  
throw_exception,boost::safe_numerics::ignore_exception> >,std::ratio<1,1000000000>   
>,std::chrono::duration<boost::safe_numerics::safe_base<int,-2147483648,2147483647,boost::  
safe_numerics::native,boost::safe_numerics::exception_policy<boost::safe_numerics::throw_exception,  
boost::safe_numerics::throw_exception,boost::safe_numerics::throw_exception,boost::  
safe_numerics::ignore_exception> >,std::ratio<1,1000000000> > >':   
recursive alias declaration  
```

---

## Comment 2

> Username: robertramey  
> Created at: 2023-10-06 17:07:14 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/135#issuecomment-1751121424  

I don't have an obvious answer to the above.  I'd have to look into it more detail which right now I don't have time for.  
  
But a few things I see right away so here a couple of suggestions:  
  
a) I generally try to avoid the usage of auto.  I realize it's convenient, but it hides sometimes surprising type conversions.  
  
b) usage of contracts like Int {1} are problematic.  The intention is obviously to create a safe constant.  This is of tremendous utility.  First it would mean that save operations can be constexper.  Second, the safe numerics library keeps track of the variable range at compile time in order to detect operations which can never fail and hence don't need to be checked.  BUT Int{1} creates a safe type with the range of min, max which is the wrong range.  So we define a special type for constants.  This is explained here: https://www.boost.org/doc/libs/1_83_0/libs/safe_numerics/doc/html/eliminate_runtime_penalty/2.html  
Read and understand this and I believe that you'll get better results.

---

## Comment 3

> Username: christianbrugger  
> Created at: 2023-10-07 05:55:28 UTC  
> Updated at: 2023-10-07 05:57:23 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/135#issuecomment-1751612385  

I understand this is not a simple error message to work on, as gives no real information and it might also not be an easy fix.  
  
Regarding `std::chrono` and safe constants... Well, this is a completely different can of worms. One would expect this to compile:  
  
```c++  
int main()  
{  
    constexpr const ConstDuration<1> v1;  // chrono duration with safe_signed_literal  
    constexpr const ConstDuration<2> v2;  
  
    auto res = v1 + v2;  //  no match for 'operator+'  
  
    return res.count();  
}  
```  
  
But it doesn't, neither on Clang, GCC nor MSVC. [See full example.](https://godbolt.org/z/YEznx5MW1)  
  
MSVC gives the most interesting error message:  
```  
'type': is not a member of any direct or indirect base class of 'std::common_type<_Rep1,_Rep2>'  
```  
  
This is also mentioned in [pending issues](https://www.boost.org/doc/libs/develop/libs/safe_numerics/doc/html/pending_issues.html).  
  
The initial example I gave does not trigger this, as we are not converting between different chrono types. Its another problem with the operators. Hover any conversion in rep (representation), or period of a chrono object is triggering the above, like going from nano- to microseconds, or from int to safe-int. I think this also prevents safe literals from working.  
  
I am not sure from the wording of the pending issue, if the user is expected to implement `std::common_type` for the safe types he is using, but I guess its not straightforward.  
  
----  
  
As of now:  
* For GCC and Cang `std::chronos` the math-operators are usable with a single safe-int type. Conversions between different chrono types need to be done manually.  
* For MSVC, right now the only construction of safe-int chronos objects is supported. Do do math, one needs to retrieve the safe-int from the chrono object, do the calculations on the underlying type and then construct again a `std::chrono::duration` object with the right period.
