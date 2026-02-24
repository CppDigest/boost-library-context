# #677 - standalone: c++ usage in preprocessor evaluation [Closed]

> Username: michael-herwig  
> Created at: 2021-08-23 08:31:30 UTC  
> Updated at: 2021-08-24 16:02:20 UTC  
> Closed at: 2021-08-24 14:36:44 UTC  
> Url: https://github.com/boostorg/math/issues/677  

Compiling for windows on arm64 in standalone mode, I get an error `fatal error C1012: unmatched parenthesis: missing ')'` at https://github.com/boostorg/math/blob/564967785f2d01f755b1a38bb1f71c3484992d87/include/boost/math/special_functions/detail/fp_traits.hpp#L247-L253, causing the preprocessor to evaluate a c++ expression from https://github.com/boostorg/math/blob/564967785f2d01f755b1a38bb1f71c3484992d87/include/boost/math/special_functions/detail/fp_traits.hpp#L37-L43  
  
I use the arm64 compiler shipped with Visual Studi 2022 Preview 3: `Microsoft (R) C/C++ Optimizing Compiler Version 19.30.30423 for ARM64`. To reproduce the error one may compile using the STL version including the `<bit>` header, [see github](https://github.com/microsoft/STL). To reproduce the error it is sufficient to include the corresponding header  
```c++  
#include "boost/math/special_functions/detail/fp_traits.hpp"  
int main() { return 0; }  
```  
and compile it from the `arm64` development environment (ie. `cl /nologo -DBOOST_MATH_STANDALONE=1 -Istl\inc -Iboost-math\include /std:c++latest boost-math.cpp`).

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-08-23 14:49:49 UTC  
> Url: https://github.com/boostorg/math/issues/677#issuecomment-903843385  

@mborland : Do you have a Windows machine that you can validate a fix on?

---

## Comment 2

> Username: mborland  
> Created at: 2021-08-23 15:01:39 UTC  
> Url: https://github.com/boostorg/math/issues/677#issuecomment-903853068  

> @mborland : Do you have a Windows machine that you can validate a fix on?  
  
I have a windows machine, but I don't have any kind of development environment setup. I'll grab a copy of the referenced visual studio to try and get it to work.

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-08-23 15:34:36 UTC  
> Updated at: 2021-08-23 15:34:56 UTC  
> Url: https://github.com/boostorg/math/issues/677#issuecomment-903880641  

@michael-herwig : My apologies for the rarely-acceptable "submit a PR" trope, but if it's just closing a parenthesis it might get you unblocked faster . . . looks like we might have some time to get your environment set up.

---

## Comment 4

> Username: michael-herwig  
> Created at: 2021-08-23 15:44:02 UTC  
> Url: https://github.com/boostorg/math/issues/677#issuecomment-903890396  

No worries, I don't think it's a closing parenthesis. As far as I can see, it's the endianness detection stated as a macro used in a preprocessor `#if` directive. Actually, the offset(s) configured later are the only occurrences I found using standalone endianness detection. Thus one may replace that with a constexpr function or variable that depends on macros or the `::std::endian` value based on the environment. I can set up a pull request later if you like or help set up the environment; let me know

---

## Comment 5

> Username: StephanTLavavej  
> Created at: 2021-08-24 00:03:14 UTC  
> Url: https://github.com/boostorg/math/issues/677#issuecomment-904216048  

I'm working on a fix.

---

## Comment 6

> Username: NAThompson  
> Created at: 2021-08-24 16:02:20 UTC  
> Url: https://github.com/boostorg/math/issues/677#issuecomment-904771518  

Release cut with the fix from @StephanTLavavej .  
  
Thanks for your help!
