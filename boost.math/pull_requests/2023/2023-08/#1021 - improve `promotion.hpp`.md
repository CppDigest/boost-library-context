# #1021 improve `promotion.hpp` [Closed]

> Username: ryanelandt  
> Created at: 2023-08-26 01:46:02 UTC  
> Updated at: 2023-09-09 16:58:41 UTC  
> Closed at: 2023-09-09 16:57:04 UTC  
> Url: https://github.com/boostorg/math/pull/1021  

This PR attempts to address some of the issues raised in #1020 including:  
- Emulation of variadic templates with optional `float` arguments  
- Lack of support for 16 bit floating types  
- Reliance on manual specialization  
  
This PR mostly refactors the promotion functionality to make how the system works more clear. Design choices that change the promotion behavior break too many tests and are out of the scope of this PR. Also, this refactor seemed to have no effect on the time required to compile and run the unit tests in Boost Math.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-08-26 11:25:54 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1694276118  

This all looks good to me, and is pretty much how I would rewrite things here had I the time ;)  
  
Some quick comments:  
  
* std::is_floating_Point_v<__float128> may be either true or false depending on the -std= option in play, we probably don't currently have tests for case that -std=c++23 rather than gnu++23 which is why is passing in that case (ie tests for when the type __float128 exists but BOOST_HAS_FLOAT128 is *not* set because support is too limited).  I don't know if this matters or not!  
* The failing tests are because `promote_args_t<float16_t>` has changed from float to float16_t - I think we could argue the case either way here - the issue really is that float16_t is a storage type and not really suitable for computation (certainly not special functions).  On the other hand, I can see a use for trivial functions like float_distance/float_next/sign etc being genuinely useful on 16-bit floats.  
* I wonder if common_type does the correct thing for all cases, including T not a floating_point type?  ie does `common_type<multiprecision_type, double>` just plain work?  How about 2 different multiprecision types?  If so we can simplify further.  A cursory check suggests this would be fine.  
* We probably need to double check this PR against the multiprecision tests before merging.

---

## Comment 2

> Username: ryanelandt  
> Created_at: 2023-08-26 19:05:28 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1694483101  

> I wonder if common_type does the correct thing for all cases, including T not a floating_point type? ie does common_type<multiprecision_type, double> just plain work? How about 2 different multiprecision types? If so we can simplify further. A cursory check suggests this would be fine.  
  
A promotion system _should_ be able to be structured in this way. And, this is where I think this promotion system should eventually end up. But it's not currently possible to do this due to the promotion system being used for four different effects in Boost Math.  
 1. **Promotion** in the sense that one expects (e.g., std::common_type)  
 2. **Integer doublification**, the conversion of all integer types to double. This results in the promotion of sin(1) to sin(1.0) which may be what the user wants.  
 3. **Asymmetric conversion rule** extends promotion to types that cannot be promoted in the conventional sense. This functionality is used in order for promotion to be applied to e.g., boost::math::policies::policy  
 4. **`float` enforcement** some special math functions don't work well, or at all with float16_t. So, the promotion system is used to convert float16_t to float for these cases.  
      
IMO, these 4 operations should be handled in 4 different parts of the code  
 1. **Promotion** --> `promote_args`  
 2. **Integer doublification** --> `promote_args_with_int_as_double` (new method)  
 3. **Asymmetric conversion rule** --> some sort of refactor affecting `boost::math::policies::policy`not sure what this would look like yet.  
 4. **`float` enforcement** --> refactor `boost::math::policies::policy` to have an operation-specific minimum float type. Previously this was assumed to be `float`. The the important aspect is that enforcing minimum float types is the responsibility of the policy system, not the promotion system.  
  
  
To address the previous comments:  
  
> std::is_floating_Point_v<__float128> may be either true...  
  
I think this issue is relatively minor compared to other issues  
  
> The failing tests are because promote_args_t<float16_t>...  
  
I think I fixed the test issues in the latest commit. CI will have the final say.  
  
> We probably need to double check this PR against the multiprecision...  
  
They looked fine on my end. This refactor should not effect multiprecision promotion. Multiprecion types are not floating point types according to std::is_floating_point. So, their promotion uses the same asymmetric conversion rule as before.

---

## Comment 3

> Username: ryanelandt  
> Created_at: 2023-08-26 20:47:58 UTC  
> Updated_at: 2023-08-26 20:56:09 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1694499039  

Previous promotion behavior on the policy side only ever applied promotions to types `float` and `double`: https://github.com/boostorg/math/blob/74bb4bccf231e063f25bd9d1cea5493ec261409e/include/boost/math/policies/policy.hpp#L732-L742   
  
This limitation meant that special math functions requiring internal promotion didn't work as intended for `float16_t` because internal promotions were skipped.The previous commit fixed this issue by using `promote_args`.   
  
This fix seems to have addressed the previous issues with `float16_t`, but caused new failures that revealed something that's obvious in hindsight... The promotion behavior on the policy side also never worked for `float32_t` or `float64_t`.

---

## Comment 4

> Username: ryanelandt  
> Created_at: 2023-08-26 22:11:24 UTC  
> Updated_at: 2023-08-26 22:14:50 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1694511068  

On a related note, there appear to be no specializations of `promote_args<T1,T2>` for the case where one of both of the arguments is an autodiff type. https://github.com/boostorg/math/blob/74bb4bccf231e063f25bd9d1cea5493ec261409e/include/boost/math/differentiation/autodiff.hpp#L2003-L2028  
Hopefully specializing `promote_args<T1,T2>` for autodiff types restores agreement.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2023-08-27 08:37:21 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1694607380  

> This fix seems to have addressed the previous issues with float16_t, but caused new failures that revealed something that's obvious in hindsight... The promotion behavior on the policy side also never worked for float32_t or float64_t.  
  
Matt and I talked about this at length: the conclusion was that if the user is deliberately using a fixed width floating point type then we should respect that and not carry out any internal promotion.  In contrast the float/double/long double types are of unspecified width, so playing a little fast and loose with those to ensure internal accuracy seemed sensible.  That not withstanding, the speed hit in promoting from double->long double has changed enormously since the library was written, so it may be that we'll change the default to not promote at some point.  
  
> On a related note, there appear to be no specializations of promote_args<T1,T2> for the case where one of both of the arguments is an autodiff type.   
  
Again, wouldn't simply using common_type internally in all cases solve that?  
  
> Integer doublification, the conversion of all integer types to double. This results in the promotion of sin(1) to sin(1.0) which may be what the user wants.  
  
It's also what the behaviour that the standard specifies.  
  
> float enforcement --> refactor boost::math::policies::policy to have an operation-specific minimum float type. Previously this was assumed to be float. The the important aspect is that enforcing minimum float types is the responsibility of the policy system, not the promotion system.  
  
It would be relatively easy to add a "minimum evaluation type" to the policy code, but again, I'm not sure this matters that much as float16_t really shouldn't be used for calculation purposes anyway IMO.

---

## Comment 6

> Username: ryanelandt  
> Created_at: 2023-08-27 13:50:20 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1694674001  

> Matt and I talked about this at length: the conclusion was that if the user is deliberately using a fixed width floating point type then we should respect that and not carry out any internal promotion. In contrast the float/double/long double types are of unspecified width, so playing a little fast and loose with those to ensure internal accuracy seemed sensible.  
  
According to [cppreference](https://en.cppreference.com/w/cpp/language/types):  
- float — single precision floating-point type. Matches IEEE-754 binary32 format if supported.   
- double — double precision floating-point type. Matches IEEE-754 binary64 format if supported.   
  
Given that 64 bit support is ubiquitous, I think the vast majority of users are going to expect `double` to be 64 bits. What users are not going to expect is for `double` and `float64_t` calculations to return different results as mentioned [here](https://github.com/boostorg/math/pull/978#issuecomment-1523243973) or [here](https://github.com/boostorg/math/pull/978#issuecomment-1525438174).  
  
I think that user's type expectations are primarily concerned with input/output relationships and not internal promotion. For example, if I call `sin(float16_t)`, I expect `float16_t` out, not `float32_t`. If internal promotion needs to happen to ensure speed or accuracy that's fine. It's not uncommon for algorithms to view or convert data to different formats in intermediate calculations (e.g., Kahan summation, fast inverse sqrt, etc).   
  
> Again, wouldn't simply using common_type internally in all cases solve that?  
  
There are a variety of problems with increasing the use of `std::common_type`. For example, for autodiff types it gives the wrong answer if one argument is an autodiff type, and doesn't compile for two arguments. For example:   
  
```c++  
#include "test_autodiff.hpp"  
#include <boost/math/tools/promotion.hpp>  
#include <boost/type_index.hpp>  
  
void test_promotion() {  
  using ft_f_5 = decltype(make_fvar<float, 5>(float(1)));  
  using ft_d_3 = decltype(make_fvar<double, 3>(double(1)));  
  
  using ft_promoted_a = boost::math::tools::promote_args<ft_d_3, ft_f_5>::type;  
  using ft_promoted_b = std::common_type_t<double, ft_f_5>;  
  // using ft_promoted_c = std::common_type_t<ft_f_5, ft_d_3>;  // Doesn't compile  
  
  std::cout << "ft_promoted_a: " << boost::typeindex::type_id<ft_promoted_a>().pretty_name() << "\n";  
  std::cout << "ft_promoted_b: " << boost::typeindex::type_id<ft_promoted_b>().pretty_name() << "\n";  
};  
```  
In other places in the code such as   
https://github.com/boostorg/math/blob/74bb4bccf231e063f25bd9d1cea5493ec261409e/include/boost/math/special_functions/beta.hpp#L1495-L1502  
the thing you are promoting is literally a policy. So trying to use `std::common_type` here gives you a compile error that says:  
```c++  
../../../boost/math/special_functions/binomial.hpp:73:124:   required from here  
/usr/include/c++/9/type_traits:2386:11: error: no type named ‘type’ in ‘struct std::common_type<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >’  
```  
  
> It's also what the behaviour that the standard specifies.  
  
The promotion that occurs when one calls `std::sin(1)` is operation [specific](https://en.cppreference.com/w/cpp/numeric/math/sin). It's not true generally that `int` gets promoted to `double`. For example, `std::common_type_t<int, char>` gives `int` not `double`. Some operations such as `+` work on integers.  
  
> It would be relatively easy to add a "minimum evaluation type" to the policy code, but again, I'm not sure this matters that much as float16_t really shouldn't be used for calculation purposes anyway IMO.  
  
I think what some people may want is to be able to have their float type of choice be both the input and output types of various functions and for these calculations to be reasonably accurate. They don't want to have to manually convert types to get good answers. If promotion is required internally to maintain reasonable input/output accuracy, this is fine. Minimizing user effort is a design goal. Maybe @gpeterhoff can weigh in here as a `std::bfloat16_t` user.

---

## Comment 7

> Username: ryanelandt  
> Created_at: 2023-08-27 19:55:48 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1694747827  

It seems that supporting internal promotion for types that are not `float` or `double` requires further discussion. I split off the parts of this PR not related to internal promotion into #1022.

---

## Comment 8

> Username: mborland  
> Created_at: 2023-08-28 12:45:34 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1695636824  

> > Matt and I talked about this at length: the conclusion was that if the user is deliberately using a fixed width floating point type then we should respect that and not carry out any internal promotion. In contrast the float/double/long double types are of unspecified width, so playing a little fast and loose with those to ensure internal accuracy seemed sensible.  
>   
> According to [cppreference](https://en.cppreference.com/w/cpp/language/types):  
>   
> * float — single precision floating-point type. Matches IEEE-754 binary32 format if supported.  
> * double — double precision floating-point type. Matches IEEE-754 binary64 format if supported.  
>   
> Given that 64 bit support is ubiquitous, I think the vast majority of users are going to expect `double` to be 64 bits. What users are not going to expect is for `double` and `float64_t` calculations to return different results as mentioned [here](https://github.com/boostorg/math/pull/978#issuecomment-1523243973) or [here](https://github.com/boostorg/math/pull/978#issuecomment-1525438174).  
>   
> I think that user's type expectations are primarily concerned with input/output relationships and not internal promotion. For example, if I call `sin(float16_t)`, I expect `float16_t` out, not `float32_t`. If internal promotion needs to happen to ensure speed or accuracy that's fine. It's not uncommon for algorithms to view or convert data to different formats in intermediate calculations (e.g., Kahan summation, fast inverse sqrt, etc).  
>   
> > Again, wouldn't simply using common_type internally in all cases solve that?  
>   
> There are a variety of problems with increasing the use of `std::common_type`. For example, for autodiff types it gives the wrong answer if one argument is an autodiff type, and doesn't compile for two arguments. For example:  
>   
> ```c++  
> #include "test_autodiff.hpp"  
> #include <boost/math/tools/promotion.hpp>  
> #include <boost/type_index.hpp>  
>   
> void test_promotion() {  
>   using ft_f_5 = decltype(make_fvar<float, 5>(float(1)));  
>   using ft_d_3 = decltype(make_fvar<double, 3>(double(1)));  
>   
>   using ft_promoted_a = boost::math::tools::promote_args<ft_d_3, ft_f_5>::type;  
>   using ft_promoted_b = std::common_type_t<double, ft_f_5>;  
>   // using ft_promoted_c = std::common_type_t<ft_f_5, ft_d_3>;  // Doesn't compile  
>   
>   std::cout << "ft_promoted_a: " << boost::typeindex::type_id<ft_promoted_a>().pretty_name() << "\n";  
>   std::cout << "ft_promoted_b: " << boost::typeindex::type_id<ft_promoted_b>().pretty_name() << "\n";  
> };  
> ```  
>   
> In other places in the code such as  
>   
> https://github.com/boostorg/math/blob/74bb4bccf231e063f25bd9d1cea5493ec261409e/include/boost/math/special_functions/beta.hpp#L1495-L1502  
>   
>   
> the thing you are promoting is literally a policy. So trying to use `std::common_type` here gives you a compile error that says:  
> ```c++  
> ../../../boost/math/special_functions/binomial.hpp:73:124:   required from here  
> /usr/include/c++/9/type_traits:2386:11: error: no type named ‘type’ in ‘struct std::common_type<double, boost::math::policies::policy<boost::math::policies::promote_double<false>, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >’  
> ```  
>   
>  > It's also what the behaviour that the standard specifies.  
>   
> The promotion that occurs when one calls `std::sin(1)` is operation [specific](https://en.cppreference.com/w/cpp/numeric/math/sin). It's not true generally that `int` gets promoted to `double`. For example, `std::common_type_t<int, char>` gives `int` not `double`. Some operations such as `+` work on integers.  
>   
> > It would be relatively easy to add a "minimum evaluation type" to the policy code, but again, I'm not sure this matters that much as float16_t really shouldn't be used for calculation purposes anyway IMO.  
>   
> I think what some people may want is to be able to have their float type of choice be both the input and output types of various functions and for these calculations to be reasonably accurate. They don't want to have to manually convert types to get good answers. If promotion is required internally to maintain reasonable input/output accuracy, this is fine. Minimizing user effort is a design goal. Maybe @gpeterhoff can weigh in here as a `std::bfloat16_t` user.  
  
I am still not convinced we should be promoting on fixed width types. If there was some floating point version of the fast or least options that integers have that case would be different. Since you brought up `bfloat` that is a hardware optimized type:  
  
`  
The Google hardware team chose bfloat16 for Cloud TPUs to improve hardware efficiency while maintaining the ability to train deep learning models accurately, all with minimal switching costs from float32. The physical size of a hardware multiplier scales with the square of the mantissa width. With fewer mantissa bits than FP16, the bfloat16 multipliers are about half the size in silicon of a typical FP16 multiplier, and they are eight times smaller than an float32 multiplier.  
`  
  
If we start promoting `bfloat` to a 16 or 32-bit float the performance hit would be massive. There's a lot of recent literature in this space. Without promotion I think we are still well within the accuracy we need to be without unforeseen slowdowns on specialized hardware.

---

## Comment 9

> Username: ryanelandt  
> Created_at: 2023-08-28 14:09:15 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1695769115  

**Performance and `bfloat16`**  
A user might train a neural network that uses the datatype `bfloat16` on a TPU.  
They might want to inspect this `bfloat16` data on their desktop using the tools  
of Boost Math. In Boost Math, internal promotion, could/should be used when it  
is necessary to ensure the accuracy of the result. Many/most functions may not  
require it. I believe that neural networks are typically composed of simple functions  
multiplication, addition, max, etc. I'm not seeing a situation where someone would  
be using a special function in Boost Math that requires promotion on a TPU in a  
performance critical application. I may be missing some uses cases.  
  
> I am still not convinced we should be promoting on fixed width types.  
  
I'm not either. Things I am convinced of:  
 - `float64_t` and `double` should be treated identically for promotion purposes.  
 - `float32_t` and `float` should be treated identically for promotion purposes.  
 - `float16_t` should be promoted in a way that follows from `float32_t` and `float64_t`  
 - `bfloat16_t` should be promoted in a way that makes sense in the context of the  
   other fixed width float types  
  
> If we start promoting bfloat to a 16 or 32-bit float the performance hit would be massive.   
  
Currently when `promote_args` is called on a `bfloat16` or a `float16` the result will be  
at least a `float` this is enforced in two places in `promotion.hpp`:  
https://github.com/boostorg/math/blob/74bb4bccf231e063f25bd9d1cea5493ec261409e/include/boost/math/tools/promotion.hpp#L117  
https://github.com/boostorg/math/blob/74bb4bccf231e063f25bd9d1cea5493ec261409e/include/boost/math/tools/promotion.hpp#L246   
  
By changing the promotion behavior of `policy.hpp`, my motivation was to make it  
consistent with that of `promote_args`. I'm against promotion unless it is necessary  
for accuracy.

---

## Comment 10

> Username: mborland  
> Created_at: 2023-08-28 15:26:57 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1695898722  

> I'm not either. Things I am convinced of:  
>   
> * `float64_t` and `double` should be treated identically for promotion purposes.  
> * `float32_t` and `float` should be treated identically for promotion purposes.  
> * `float16_t` should be promoted in a way that follows from `float32_t` and `float64_t`  
> * `bfloat16_t` should be promoted in a way that makes sense in the context of the  
>   other fixed width float types  
>   
  
I don't think `float64_t` and `double` need to match like @jzmaddock said. GCC has take a similar position with excess precision support in GCC 13:  
  
`  
Excess precision support (which has been available in C since GCC 4.5) has been implemented for C++ as well. It is enabled by default in strict standard modes like -std=c++17, where it defaults to -fexcess-precision=standard, while in GNU standard modes like -std=gnu++20 it defaults to -fexcess-precision=fast. The option mainly affects IA-32/x86-64 using x87 math and in some cases on Motorola 68000, where float and double expressions are evaluated in long double precision and S/390, System z, IBM z Systems where float expressions are evaluated in double precision. Also, on several architectures where std::float16_t or std::bfloat16_t types are supported those are evaluated in float precision. -fexcess-precision=fast restores previous behavior.  
`  
  
I think we should continue with `doubles` being allowed to be promoted to `long double` for additional accuracy, and `float64_t` not being allowed to be promoted. Our current behavior for the 16-bit types matches theirs as well. I think you're right that there's little chance Boost.Math is used on low bit types on specialized hardware so the performance concern is minimal.

---

## Comment 11

> Username: ryanelandt  
> Created_at: 2023-08-28 17:29:57 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1696074555  

Okay, this is becoming a little more clear. I'm interested in @jzmaddock's opinions on `float64_t` and `double`.  
  
Irrespective of his perspective, what are your opinions on the promotion of autodiff types. For example, if it's policy to promote `float` to `double`,  should the same policy also promote `<autodiff<float,N>` to `<autodiff<double,N>`?

---

## Comment 12

> Username: mborland  
> Created_at: 2023-08-28 17:33:32 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1696079073  

> Okay, this is becoming a little more clear. I'm interested in @jzmaddock's opinions on `float64_t` and `double`.  
>   
> Irrespective of his perspective, what are your opinions on the promotion of autodiff types. For example, if it's policy to promote `float` to `double`, should the same policy also promote `<autodiff<float,N>` to `<autodiff<double,N>`?  
  
That makes sense to me. I would ask @pulver since my understanding of the mechanics of autodiff are limited.

---

## Comment 13

> Username: pulver  
> Created_at: 2023-08-28 17:46:18 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1696095225  

> > what are your opinions on the promotion of autodiff types. For example, if it's policy to promote `float` to `double`, should the same policy also promote `<autodiff<float,N>` to `<autodiff<double,N>`?  
>   
> That makes sense to me. I would ask @pulver since my understanding of the mechanics of autodiff are limited.  
  
From a high level perspective, that makes sense. Some specific use case(s) might help illustrate the issues, if there is some hesitancy/question about this.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2023-09-09 11:08:04 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1712485726  

> There are a variety of problems with increasing the use of std::common_type. For example, for autodiff types it gives the wrong answer if one argument is an autodiff type, and doesn't compile for two arguments.  
  
I'm not suggesting we literally replace promote_args with common_type - rather that promote_args becomes a wrapper around common type, and autodiff and other awkward cases (multiprecision expression templates for example) continue to specialize promote_args as now.  
  
>In other places in the code such as  
  
That's not so good, it's a bit of lazy C++98'ism on my part, we can do better now, but that's probably beyond the scope of this PR.  
  
> The promotion that occurs when one calls std::sin(1) is operation [specific](https://en.cppreference.com/w/cpp/numeric/math/sin). It's not true generally that int gets promoted to double. For example, std::common_type_t<int, char> gives int not double. Some operations such as + work on integers.  
  
Sure, but `promote_args` was designed for functions accepting floating point ags, it shouldn't be applied otherwise.  
  
> I am still not convinced we should be promoting on fixed width types. If there was some floating point version of the fast or least options that integers have that case would be different.   
  
Me neither to be honest.  
  
With regard to float64 and double (for example), since these are distinct types with presumably distinct uses I don't necessarily see any issues in treating them differently.

---

## Comment 15

> Username: ryanelandt  
> Created_at: 2023-09-09 16:57:04 UTC  
> Updated_at: 2023-09-09 16:58:41 UTC  
> Url: https://github.com/boostorg/math/pull/1021#issuecomment-1712554323  

I think you're saying that you would like for `promote_args` to default to `common_type`. For awkward cases such as autodiff, `promote_args` would be specialized. Assuming this is the case, the implementation in this PR is reasonably close to this ideal.  
  
> Sure, but promote_args was designed for functions accepting floating point ags, it shouldn't be applied otherwise.  
  
That's fine, perhaps this method could be renamed to `promote_args_float` or something similar.  
  
> With regard to float64 and double (for example), since these are distinct types with presumably distinct uses I don't necessarily see any issues in treating them differently.  
  
I'm more or less convinced now. The motivation behind policy-side promotion wasn't to enforce a minimum bits size for reasonable accuracy, but rather to get a little bit of extra accuracy for free. Because this isn't true anymore, it doesn't make sense to continue doing this for  the floating point types introduced in C++23.  
  
Because I didn't understand the motivation behind the policy-side promotion, the changes I made in this PR that affected policy-side promotion are wrong. I'm going to close this PR. I had previously chunked off the parts of this PR not affecting policy-side promotion into #1022.  
  
@jzmaddock I can open an issue summarizing the current conversation about policy-side promotion in case others have similar questions if you would like.

---
