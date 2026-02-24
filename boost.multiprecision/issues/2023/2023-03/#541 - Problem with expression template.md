# #541 - Problem with expression template [Closed]

> Username: afabri  
> Created at: 2023-03-10 13:25:18 UTC  
> Updated at: 2023-04-21 17:58:25 UTC  
> Closed at: 2023-04-21 17:58:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/541  

Hello,  
  
The code below does not compile with clang (but with VC2022) and boost 1_80_0.  
The arithmetic expression `2 * r` becomes an `expression<..>` and that does not convert to the rational type.  
  
Is that normal behavior, or a bug.  
  
``` cpp  
#include <boost/multiprecision/cpp_int.hpp>  
  
typedef boost::multiprecision::cpp_rational Rat;  
  
struct E {  
  Rat rat;  
  E(const Rat& rat)  
    : rat(rat)  
  {}  
};  
  
int main()  
{  
  Rat r(1,3);  
  E e = 2 * r;  
  // E e(2*r);           //compiles  
 //  E e = Rat(2*r); // compiles   
  return 0;  
}  
```  
https://gist.github.com/afabri/fcd036af9a1258b3d9d0bfd9977fd932

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-03-11 11:34:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/541#issuecomment-1464891657  

I believe this is expected behaviour - the issue being that there are **two** user-defined conversions required to construct an E from an expression template - one the conversion to Rat, and one in E's constructor.  This is certainly unfortunate, but I don't see an easy fix at our end right now.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-03-11 11:37:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/541#issuecomment-1464892171  

@mborland : this is unrelated to the changes in rational_adaptor, I can reproduce with mpfr_float as well, it's intrinsic to the use of expression templates.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2023-03-11 13:20:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/541#issuecomment-1464910980  

I might have a fix.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-03-13 18:30:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/541#issuecomment-1466704232  

This is somewhat fixed in develop: the fix requires C++17 or later, prior to that the fix causes all sorts of issues with gcc and clang that I've been unable to fix.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2023-03-24 18:48:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/541#issuecomment-1483262588  

This fix has been reverted as causing too many issues.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2023-04-21 17:58:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/541#issuecomment-1518160828  

I'm going to close this as unfixable, consider:  
  
```  
#include <boost/multiprecision/cpp_int.hpp>  
#include <cmath>  
  
template <class Num>  
struct wrapper  
{  
   wrapper(const Num&);  
};  
  
using N = boost::multiprecision::cpp_int;  
using W = wrapper<N>;  
  
bool foo(const N&);  
bool foo(const W&);  
  
int main(int argc)  
{  
  
   N n;  
   W w(n);  
  
   foo(n * n);  
  
   return 0;  
}  
```  
  
If we allow expression templates to convert to all the types that class `number` converts to, then this example will necessarily fail, as the call to `foo` will be ambiguous: there are two paths the compiler could take, each involving one user-defined conversion.  
  
My suggestion then, is that any class that is constructible from a number, should also be constructible from an expression template.  But since expression templates are complex undocumented types, the best way to achieve that is a template constructor which is enable_If'ed on `is_number_expression`:  
  
```  
template <class Arg, class = typename std::enable_if<boost::multiprecision::is_number_expression<Arg>::value>::type>  
myclass(const Arg&);  
```  
  
Hope that all makes sense!
