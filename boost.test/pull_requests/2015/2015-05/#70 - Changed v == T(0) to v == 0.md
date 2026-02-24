# #70 Changed v == T(0)  to  v == 0 [Merged]

> Username: akrzemi1  
> Created at: 2015-05-28 15:15:28 UTC  
> Updated at: 2015-05-29 08:36:42 UTC  
> Merged at: 2015-05-29 08:36:30 UTC  
> Closed at: 2015-05-29 08:36:30 UTC  
> Url: https://github.com/boostorg/test/pull/70  

With this change we get a _desired_ compile time failure when we are passed type `T` that is not implicitly convertible from `int` but is explicitly convertible. Now, we make an implicit conversion from `int` a requirement and refuse to compile otherwise.  
  
This addresses an issue where the user gives us a type `T` with non-intuitive construction from `int`.

---

## Comment 1

> Username: rogeeff  
> Created_at: 2015-05-28 15:21:59 UTC  
> Url: https://github.com/boostorg/test/pull/70#issuecomment-106403217  

I thought there was the reason we added these casts? Something about  
special case foe multiprecison lib  
  
On Thu, May 28, 2015 at 11:15 AM, Andrzej Krzemieński <  
notifications@github.com> wrote:  
  
> With this change we get a _desired_ compile time failure when we are  
> passed type T that is not implicitly convertible from int but is  
> explicitly convertible. Now, we make an implicit conversion from int a  
> requirement and refuse to compile otherwise.  
>   
> This addresses an issue where the user gives us a type T with  
>   
> ## non-intuitive construction from int.  
>   
> You can view, comment on, or merge this pull request online at:  
>   
>   https://github.com/boostorg/test/pull/70  
> Commit Summary  
> - Changed v == T(0) to v == 0  
>   
> File Changes  
> - _M_ include/boost/test/tools/fpc_op.hpp  
>   https://github.com/boostorg/test/pull/70/files#diff-0 (4)  
>   
> Patch Links:  
> - https://github.com/boostorg/test/pull/70.patch  
> - https://github.com/boostorg/test/pull/70.diff  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/test/pull/70.  
  
##   
  
Gennadiy Rozental

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2015-05-28 15:37:10 UTC  
> Url: https://github.com/boostorg/test/pull/70#issuecomment-106419262  

All the Boost numeric types I am aware of (including big ints and big decimals) offer an implicit conversion from `int`, so they will not be affected.  
  
At some point we were discussing the following example:  
  
> Issue 1.  
> Consider the following implementation of a "fixed point" type:  
>   
> class fixed_point  
> {  
>   const int denominator_exponent;  
>   int numerator;  
>   
> public:  
>   explicit fixed_point(const std:string& text_representation);  
>   explicit fixed_point(int de = 8, int n = 256)  
>     : denominator_exponent(de), numerator(n) {}  
> };  
>   
> The "recommended" way of assigning this type an initial value is to use a string or a C++11 user-defined literal:  
> fixed_point x = 11e5_xf; // which calls fixed_point(5, 11);  
> But we also provide the "manual" constructor. It is admittedly strange, but since it is explicit, no-one will confuse it with anything.  
>   
> Now, if I specialize math::fpc::tolerance_based because i want to use it with tolerance comp, I will get unpredictable results, because fixed_point(0) does not represent a zero value.  
>   
> Current result: compiles, and produces unexpected results.  
> Expected result: fails to compile  
  
This case (plausible IMO) is fixed by the proposed change.

---

## Comment 3

> Username: akrzemi1  
> Created_at: 2015-05-28 16:10:17 UTC  
> Url: https://github.com/boostorg/test/pull/70#issuecomment-106451190  

I can see that in the proposed change for operator< the code already makes a naked comparison `0 < fpt`. I believe that the expectation that FPT is implicitly convertible from `int` is quite natural.

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2015-05-28 19:33:34 UTC  
> Updated_at: 2015-05-28 19:34:01 UTC  
> Url: https://github.com/boostorg/test/pull/70#issuecomment-106574444  

does `0 < fpt` induce `fpc::tolerance_based<Lhs>::value && fpc::tolerance_based<Rhs>::value == true`, with `Lhs` the type of `0`? I should check that I am executing this floating point comparison and not the native/default one.

---

## Comment 5

> Username: akrzemi1  
> Created_at: 2015-05-28 20:16:52 UTC  
> Url: https://github.com/boostorg/test/pull/70#issuecomment-106584287  

> does `0 < fpt` induce `fpc::tolerance_based<Lhs>::value && fpc::tolerance_based<Rhs>::value == true`, with `Lhs` the type of `0`? I should check that I am executing this floating point comparison and not the native/default one.  
  
I do not understand your concern here. Anyway, do you perceive any danger related with the change in this PR?

---

## Comment 6

> Username: raffienficiaud  
> Created_at: 2015-05-28 20:22:17 UTC  
> Url: https://github.com/boostorg/test/pull/70#issuecomment-106585335  

I do not perceive any danger here. I was just surprised that the expression `0 < fpt` was using the floating point comparison in the file `fpc_op.hpp` after reading your remark.

---

## Comment 7

> Username: raffienficiaud  
> Created_at: 2015-05-28 20:22:27 UTC  
> Url: https://github.com/boostorg/test/pull/70#issuecomment-106585363  

I have to merge the PR manually though.

---

## Comment 8

> Username: raffienficiaud  
> Created_at: 2015-05-29 08:36:42 UTC  
> Url: https://github.com/boostorg/test/pull/70#issuecomment-106745440  

merged already

---
