# #270 Extensions: define ttmath big missing arithmetic operators [Merged]

> Username: mkaravel  
> Created at: 2015-03-14 00:20:14 UTC  
> Updated at: 2015-04-28 13:44:00 UTC  
> Merged at: 2015-03-22 14:12:20 UTC  
> Closed at: 2015-03-22 14:12:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/270  

The following simple program fails:  
  
```  
#include <boost/geometry/extensions/contrib/ttmath_stub.hpp>  
  
template <typename T>  
void f(T const&, T const&)  
{  
}  
  
template <typename T>  
void test()  
{  
    T one(1.0);  
  
    f(one + one, one);  
    f(one * one, one);  
    f(one / one, one);  
}  
  
int main()  
{  
    test<ttmath_big>();  
  
    return 0;  
}  
```  
  
The reason is that the result of `operator+`, `operator*` and `operator-` is an object of type `::ttmath::Big<1,4>` instead of `::ttmath_big` (`::tmath_big` inherits from `::ttmath::Big<1,4>`).  
  
In this PR I define the missing operators, as well as the unary `operator+` (for completeness) so that the bug above is gone.  
  
This PR should be merged after boost 1.58 is released so that we won't have to worry about extensions right now.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-03-14 18:38:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/270#issuecomment-80661611  

Indeed it's a reasonable change. Btw, it's advised to implement operators as free/friend functions because then passed objects of derived classes can be implicitly casted.  
  
Why is the unary `operator+` needed? Does the lack of it breaks some possible use cases?  
  
Aside from this PR. I think we could just support the `ttmath::Big<>` which AFAIR is not supported only because value initialization doesn't set it to 0. If a value must be initialized to 0 it should be explicitly initialized to 0 in the library. We shouldn't require that a default ctor sets a user-defined numeric type to 0.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-03-16 08:13:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/270#issuecomment-81482958  

The unary `operator+` is needed for completeness.  
For example consider a modified version of the program I have above:  
  
```  
#include <boost/geometry/extensions/contrib/ttmath_stub.hpp>  
  
template <typename T>  
void f(T const&, T const&)  
{  
}  
  
template <typename T>  
void test()  
{  
    T one(1.0);  
  
    f(one + one, one);  
    f(one * one, one);  
    f(one / one, one);  
    f(+one / one, one);  
}  
  
int main()  
{  
    test<ttmath_big>();  
  
    return 0;  
}  
```  
  
Unless the unary `operator+` is defined the program above fails to compile. The problem is related to the call `f(+one / one, one)`; the unary `operator+` is called and if it is not defined the program fails to compile.  
  
I agree that this operator is rarely used, but in the spirit of adding complete support for arithmetic operators I thought that it has to be there.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-03-16 08:15:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/270#issuecomment-81483656  

About your comment that operators should be free/friend functions: sure I can make this change.  
  
About initialization with `0`: this a discussion we have been having for quite some time now. It is orthogonal to this PR. I propose we discuss it on the mailing list.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-03-16 08:28:42 UTC  
> Updated_at: 2015-03-16 08:28:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/270#issuecomment-81497890  

Binary arithmetic operators are now defined as free functions; see commit 6dff815.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-03-22 10:24:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/270#issuecomment-84582067  

I'm OK with merging

---
