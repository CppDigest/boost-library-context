# #57 Customization point for FP types not convertible from int [Closed]

> Username: akrzemi1  
> Created at: 2015-05-12 22:19:23 UTC  
> Updated at: 2015-05-19 20:05:37 UTC  
> Closed at: 2015-05-19 20:04:14 UTC  
> Url: https://github.com/boostorg/test/pull/57  

I am not 100% if it does not break anything, so please check if this change is acceptable, and if all tests pass.  
  
The idea behind the change is that the floating point utils work even if the floating-point type is not default constructible, and is not convertible from int. In that case, the user has to specialize function:  
  
```  
boost::math::fpc::floating_point_from_int<UDT>(int);  
```

---

## Comment 1

> Username: rogeeff  
> Created_at: 2015-05-13 05:09:15 UTC  
> Url: https://github.com/boostorg/test/pull/57#issuecomment-101514783  

Why don't we just change all the literals into constructor calls? 0.01 =>  
FPT(0.01)  
  
On Tue, May 12, 2015 at 6:19 PM, Andrzej Krzemieński <  
notifications@github.com> wrote:  
  
> I am not 100% if it does not break anything, so please check if this  
> change is acceptable, and if all tests pass.  
>   
> The idea behind the change is that the floating point utils work even if  
> the floating-point type is not default constructible, and is not  
> convertible from int. In that case, the user has to specialize function:  
>   
> boost::math::fpc::floating_point_from_int<UDT>(int);  
>   
> ---  
>   
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/test/pull/57  
> Commit Summary  
> - Customization point for FP types not convertible from int  
>   
> File Changes  
> - _M_ include/boost/test/tools/detail/tolerance_manip.hpp  
>   https://github.com/boostorg/test/pull/57/files#diff-0 (2)  
> - _M_ include/boost/test/tools/floating_point_comparison.hpp  
>   https://github.com/boostorg/test/pull/57/files#diff-1 (22)  
> - _M_ include/boost/test/tools/fpc_op.hpp  
>   https://github.com/boostorg/test/pull/57/files#diff-2 (6)  
> - _M_ include/boost/test/tools/fpc_tolerance.hpp  
>   https://github.com/boostorg/test/pull/57/files#diff-3 (4)  
>   
> Patch Links:  
> - https://github.com/boostorg/test/pull/57.patch  
> - https://github.com/boostorg/test/pull/57.diff  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/57.  
  
##   
  
Gennadiy Rozental

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2015-05-13 06:57:44 UTC  
> Updated_at: 2015-05-13 07:48:24 UTC  
> Url: https://github.com/boostorg/test/pull/57#issuecomment-101538936  

This is to cover legitimate user-defined floating-point types that do not have a constructor taking type `int` or `float` or do not even have a default constructor. Or that have these constructors but with unexpected semantics. Such types are not academic examples. Consider the following plausible use cases:  
  
(1) (Example from Raffi) A fixed-exponent type:   
  
```  
template <int exponent_in_denominator>  
class fixed_point  
{  
  int numerator;  
public:  
  explicit fixed_point(int numerator) : numerator(numerator) {}  
  // ...  
};  
```  
  
The constructor taking an `int` sets the numerator, the denominator is stored as part of the type. Expression:  
  
```  
T x(1);  
```  
  
works, but its numeric value is not 1. Also, the following works:  
  
```  
T x(0.01);  
```  
  
Because `double` is first converted to `int`, but the result is even more confusing.  
  
(2)  
 for a user-defined infinite-precision binary float, I only want to allow constructors taking a `std::string` and a C++ compile-time user literal that exactly represents the value I will be storing later. I do not want the conversion from `double` as it might be inexact.

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2015-05-13 07:44:14 UTC  
> Url: https://github.com/boostorg/test/pull/57#issuecomment-101553725  

Note how the proposed change handles the Raffi's fixed-exponent example:  
  
First, when you try to use type `fixed_point<5>`, you get a compile-time error, because the default implementation of `boost::math::fpc::floating_point_from_int` uses _only_ implicit conversions, so the explicit constructor is ignored.  
  
Second, the user has now the chance to customize the framework:  
  
```  
namespace boost{ namespace math{ namespace fpc{  
  
template <int exp>  
 fixed_point<exp> floating_point_from_int< fixed_point<exp> >(int v)  
 {  
    return fixed_point<exp>(ipow(2, exp));  
 }  
}}}  
```

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2015-05-15 18:37:28 UTC  
> Updated_at: 2015-05-15 18:37:43 UTC  
> Url: https://github.com/boostorg/test/pull/57#issuecomment-102487788  

I would say that my (Raffi) example is half buggy:  
- I consider that float is a superset of int, because mathematically the set of natural numbers are contained in the set of real numbers. So this is perfectly legitimate to require a float be constructible from an int.  
- even if the constructor of my example is erroneous (because the scale on which it is working is with fixed exponent already applied), the tolerance I was setting was on the same scale and expressed using this construction. There would be a bug if, in the framework at some point, those dimensions manipulated by the user "magically" change (constructing a `fixed_point` with a `int(1)`) but from what I saw on my example, there was not such as problem as long as the tolerance is not expressed in percentage.  
- the percentage means that the dimensions of the manipulated floating points are the same. This was not the case for the `fixed_point`, which indicates that the framework is expected the floating point be constructible from an integer meaning a percentage.   
  
Checking the code, this `0.01` multiplication is performed all over the place. However, I would say that this should rather be delegated to the appropriate class. There is a `percent_tolerance_t` to which we may add a member `apply` (or whatever suitable name):  
  
`FPT percent_tolerance_t::apply(FPT value);`  
  
that might transform my floating point and a tolerance expressed in percentage into the appropriate value. The user should then override the default implementation to get his value right.   
  
In your example, I would say that the if constructed with an integer, it should be casted to the string that represents this integer. I do not think the requirement is absurd.   
  
All in all, I have mixed feelings about the content of the PR. The problem you pinned is definitely worth spending time and effort.

---

## Comment 5

> Username: akrzemi1  
> Created_at: 2015-05-15 19:18:02 UTC  
> Url: https://github.com/boostorg/test/pull/57#issuecomment-102498201  

I agree with your resentment towards the PR as it is. Now I come to think the customization in form of partial function specialization would not work for some compilers.  
  
I disagree, however, with your expectation that a user-defined floating-point type should be implicitly convertible from int. The example above is a somewhat changed version of your original example. Let me quote it again here:  
  
``` c++  
template <int exponent_in_denominator>  
class fixed_point  
{  
  int numerator;  
public:  
  explicit fixed_point(int numerator) : numerator(numerator) {}  
  // ...  
};  
```  
  
I do not consider it buggy at all. It was the user's choice not to offer a conversion from `int`. He needed a constructor where he could specify the numerator. He knew that if this constructor was used to (implicitly) convert from `int` to `fixed_point` it would be a disaster, therefore he made it `explicit`.  
  
It is the framework that made a mistake of using the explicit constructor and making an unjustified assumption. I think this is what `explicit` is for.   
  
It looks like the point of disagreement is about whether a type that claims to be a "floating-point" type should be implicitly constructible from `int`.  
  
My PR proves that it is possible to work with FP types without this assumption.

---

## Comment 6

> Username: rogeeff  
> Created_at: 2015-05-16 07:01:59 UTC  
> Url: https://github.com/boostorg/test/pull/57#issuecomment-102581082  

Whether of not it is possible is irrelevant IMO. We can't handle every  
possible whim of our users. This introduces unnecessary complication for  
the purely theoretical gain. IMO We should concentrate on more or less  
realistic types. Converting to/from float/int is the requirement.  
  
As for factoring out multiplication by 0.01, it is only in two places now.  
I am not sure it deserves level of indirection.  
  
On Fri, May 15, 2015 at 3:18 PM, Andrzej Krzemieński <  
notifications@github.com> wrote:  
  
> I agree with your resentment towards the PR as it is. Now I come to think  
> the customization in form of partial function specialization would not work  
> for some compilers.  
>   
> I disagree, however, with your expectation that a user-defined  
> floating-point type should be implicitly convertible from int. The example  
> above is a somewhat changed version of your original example. Let me quote  
> it again here:  
>   
> template <int exponent_in_denominator>class fixed_point  
> {  
>   int numerator;public:  
>   explicit fixed_point(int numerator) : numerator(numerator) {}  
>   // ...  
> };  
>   
> I do not consider it buggy at all. It was the user's choice not to offer a  
> conversion from int. He needed a constructor where he could specify the  
> numerator. He knew that if this constructor was used to (implicitly)  
> convert from int to fixed_point it would be a disaster, therefore he made  
> it explicit.  
>   
> It is the framework that made a mistake of using the explicit constructor  
> and making an unjustified assumption. I think this is what explicit is  
> for.  
>   
> It looks like the point of disagreement is about whether a type that  
> claims to be a "floating-point" type should be implicitly constructible  
> from int.  
>   
> My PR proves that it is possible to work with FP types without this  
> assumption.  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/57#issuecomment-102498201.  
  
##   
  
Gennadiy Rozental

---
