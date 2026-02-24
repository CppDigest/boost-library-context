# #785 - Boots math tools nothrow exception [Closed]

> Username: ckormanyos  
> Created at: 2022-05-12 08:26:28 UTC  
> Updated at: 2022-11-25 20:18:09 UTC  
> Closed at: 2022-11-25 18:18:23 UTC  
> Url: https://github.com/boostorg/math/issues/785  

When I am putting math on systems lacking exception support, I keep encountering this line [here](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/tools/throw_exception.hpp#L18).  
  
The conditions I would like are:  
- Have `BOOST_MATH_STANDALONE`  
- And Have `BOOST_NO_EXCEPTIONS`  
  
But for some reason, the line mentioned above keeps wanting to throw something.  
  
Is there any way (or is this even the right place) to offer an option there for the presence (or lack thereof) of `BOOST_NO_EXCEPTIONS` such that nothing is ever actually `throw`n?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-05-12 11:29:00 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1124877480  

I think perhaps we've goofed there.  
  
Is this something that can be addressed by the policy support - ie by specifying a policy that will never throw anyway?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-05-12 12:35:33 UTC  
> Updated at: 2022-05-12 12:36:40 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1124939194  

> Is this something that can be addressed by the policy support  
  
In my investigations with a lot of bare metal attempts, I have thought that you could either:  
- Cut the preprocessor macro at the end of the chain in [`tools/throw_exception.hpp`](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/tools/throw_exception.hpp#L18)  
- Add some specializations (as John mentions) in [`policies/policy.hpp`](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/policies/policy.hpp)  
- Refactor [`policies/error_handling.hpp`](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/policies/error_handling.hpp)  
  
Somehow, I'd like to do a combination of the second and third, because there are two things I do not like about `policies/error_handling.hpp`. Firstly it includes `<sstream>` which is very heavy. I would like to optionally have that gone on the metal. Secondly, I'm not sure if the support for _no_ _RTTI_ is completely consistent throughout that file.  
  
I do not, however, have the expertize needed to decide on the right scope of changes, nor the necessary skills to handle the scope of these changes alone.  
  
But these are, in fact, the two main areas (other than the plethora of warnings, which I will address in separate threads) that hamper lightweight, bare-metal progress of Math.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-05-12 16:39:17 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1125206062  

>  
>  
>     Is this something that can be addressed by the policy support  
>  
> In my investigations with a lot of bare metal attempts, I have thought that you could either:  
>  
>     Cut the preprocessor macro at the end of the chain in [tools/throw_exception.hpp](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/tools/throw_exception.hpp#L18)  
>     Specializations in [policies/policy.hpp](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/policies/policy.hpp)  
  
You shouldn't really need to do that.  
>  
>     Refactor [policies/error_handling.hpp](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/policies/error_handling.hpp)  
>  
> Somehow, I'd like to do a combination of the second and third, because there are two things i do not like about policies/error_handling.hpp. Firstly it include< <sstream> which is very heave and I would like to optionally have that gone on the metal. Secondly, I'm not sure if the support for no RTTI is completely consistent throughout that file.  
Is there an easy way to test this with regular mingw gcc?  
>  
> I do not, however, have the expertize needed to decide on the right scope of changes, nor the necessary skills to handle the scope of these changes alone.  
>  
> But these are, in fact, the two main areas (other than the plethora of warnings, which I will address in separate threads) that hamper lightweight, bare-metal progress of Math.  
  
It's the intention of that whole policy mechanism, to handle these sorts of situations, so we/I should probably try and get this right.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2022-05-12 17:27:21 UTC  
> Updated at: 2022-05-12 17:28:29 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1125247866  

Well, the file I ended up selecting for patching to get _on the metal_ is `error_handling.hpp`, which I patched [in my real-time-cpp repo](https://github.com/ckormanyos/real-time-cpp/blob/master/ref_app/src/util/boost_metal_bindings/boost/math/policies/error_handling.hpp).  
  
I found this to be a good compromise to be in the middle (i.e., not at the the beginning or end) of the chain of heavy-weight features.  
  
Optimally, a patch of the policiies themselves is most important to lighten the dependency load. We can negotiate about string-streaming in a second effort.  
  
It is really great how well Math is getting clean and localized in the past few years. It is a great project. Thanks for keeping it going!  
  
Cc: @mborland and @NAThompson

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-05-12 18:06:05 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1125281540  

What specifically is the issue at present?  I tried:  
  
```  
#include <boost/math/special_functions/gamma.hpp>  
#include <stdio.h>  
  
int main()  
{  
   printf("%g", boost::math::tgamma(-1.0));  
}  
```  
  
Compiling with ` -fno-exceptions -Wall -g -fno-rtti`  
  
And I see no warnings or errors from the build, and the program runs and prints "nan".  
  
Stepping through the error handling, I confirmed that no error message formatting is performed, neither std strings nor iostreams are used, we just set ::errno and return.  Of course this isn't standalone, and I'm relying on Boost.Config to set the necessary macros.  Plus it's hardly a comprehensive test ;)

---

## Comment 6

> Username: ckormanyos  
> Created at: 2022-05-12 18:20:05 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1125292891  

>  I see no warnings or errors from the build, and the program runs and prints "nan".  
  
I'm glad you asked that, John. Let me get an exact issue case here if I can.  
  
Recently I had [this experience](https://github.com/ckormanyos/wide-decimal/runs/6385496089?check_suite_focus=true#step:6:102).  
  
Also my syntax checkers kept reporting uncaught exceptions. One syntax checker, in particular, which does a range scan of input parameters.  
  
Let's agree to the following: If I can come up with an exact case of non-linking or other run-time problem on a common compiler that you have, I will report. If I can't come up with such a case in a reasonable time, then we close (until someday I can). Give me a bit of time for this. OK?

---

## Comment 7

> Username: ckormanyos  
> Created at: 2022-05-12 19:22:33 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1125343289  

Hi John (@jzmaddock), independently from your recent PR, I contrived a file that elicits compile-time exception errors.  
  
It is a non-trivial calculation of associated Legendre Functions. This has been chosen, since that's where I first observed this recently.  
  
The entire code is a bit of a tongue-twister.  
  
Compile with something like:  
  
```  
g++ -O2 -fno-exceptions -Wall -Wextra -isystem /mnt/c/boost/modular_boost/boost/libs/multiprecision/include -isystem /mnt/c/boost/modular_boost/boost/libs/math/include -isystem /mnt/c/boost/modular_boost/boost/libs/config/include test.cpp -o test.exe  
```  
  
ENTIRE CODE  
  
```  
///////////////////////////////////////////////////////////////////  
//  Copyright Christopher Kormanyos 2020 - 2022.                 //  
//  Distributed under the Boost Software License,                //  
//  Version 1.0. (See accompanying file LICENSE_1_0.txt          //  
//  or copy at http://www.boost.org/LICENSE_1_0.txt)             //  
///////////////////////////////////////////////////////////////////  
  
// cd /mnt/c/Users/User/Documents/Ks/PC_Software/Test  
// g++ -O2 -fno-exceptions -Wall -Wextra -isystem /mnt/c/boost/modular_boost/boost/libs/multiprecision/include -isystem /mnt/c/boost/modular_boost/boost/libs/math/include -isystem /mnt/c/boost/modular_boost/boost/libs/config/include test.cpp -o test.exe  
  
#include <iomanip>  
#include <iostream>  
  
#if !defined(BOOST_MATH_STANDALONE)  
#define BOOST_NO_EXCEPTIONS  
#endif  
  
#if !defined(BOOST_MATH_STANDALONE)  
#define BOOST_MATH_STANDALONE  
#endif  
  
#if !defined(BOOST_MP_STANDALONE)  
#define BOOST_MP_STANDALONE  
#endif  
  
#include <boost/multiprecision/cpp_dec_float.hpp>  
#include <boost/math/special_functions/gamma.hpp>  
  
namespace math { namespace wide_decimal {  
  
auto example009a_boost_math_standalone() -> bool;  
  
} // namespace wide_decimal  
} // namespace math  
  
namespace example009a_boost  
{  
  constexpr std::int32_t wide_decimal_digits10 = INT32_C(1001);  
  
  using dec1001_t = boost::multiprecision::number<boost::multiprecision::cpp_dec_float<1001, std::int64_t, std::allocator<void>>,  
                                                  boost::multiprecision::et_off>;  
} // namespace example009a_boost  
  
using namespace example009a_boost;  
  
namespace math { namespace wide_decimal { // NOLINT(modernize-concat-nested-namespaces)  
  
auto hypergeometric_2f1(const dec1001_t& a, // NOLINT(bugprone-easily-swappable-parameters)  
                        const dec1001_t& b,  
                        const dec1001_t& c,  
                        const dec1001_t& x) -> dec1001_t  
{  
  // Compute the series representation of hypergeometric_2f1  
  // taken from Abramowitz and Stegun 15.1.1.  
  // There are no checks on input range or parameter boundaries.  
  
  dec1001_t x_pow_n_div_n_fact(x);  
  dec1001_t pochham_a         (a);  
  dec1001_t pochham_b         (b);  
  dec1001_t pochham_c         (c);  
  dec1001_t ap                (a);  
  dec1001_t bp                (b);  
  dec1001_t cp                (c);  
  
  dec1001_t h2f1 = 1 + (((pochham_a * pochham_b) / pochham_c) * x_pow_n_div_n_fact);  
  
  const dec1001_t tol = std::numeric_limits<dec1001_t>::epsilon() * x;  
  
  // Series expansion of hyperg_2f1(a, b; c; x).  
  for(auto n = static_cast<std::int32_t>(2); n < INT32_C(100000); ++n)  
  {  
    x_pow_n_div_n_fact *= x;  
    x_pow_n_div_n_fact /= n;  
  
    pochham_a *= ++ap;  
    pochham_b *= ++bp;  
    pochham_c *= ++cp;  
  
    const dec1001_t term = ((pochham_a * pochham_b) / pochham_c) * x_pow_n_div_n_fact;  
  
    if((n > static_cast<std::int32_t>(INT32_C(11))) && (fabs(term) < tol))  
    {  
      break;  
    }  
  
    h2f1 += term;  
  }  
  
  return h2f1;  
}  
  
auto hypergeometric_2f1_regularized(const dec1001_t& a,  
                                    const dec1001_t& b,  
                                    const dec1001_t& c,  
                                    const dec1001_t& x) -> dec1001_t  
{  
  return hypergeometric_2f1(a, b, c, x) / boost::math::tgamma(c);  
}  
  
auto pochhammer(const dec1001_t& x, const dec1001_t& a) -> dec1001_t  
{  
  return boost::math::tgamma(x + a) / boost::math::tgamma(x);  
}  
  
} // namespace wide_decimal  
} // namespace math  
  
namespace example009a_boost  
{  
  template<typename FloatingPointType>  
  auto legendre_pvu(const FloatingPointType& v, // NOLINT(bugprone-easily-swappable-parameters)  
                    const FloatingPointType& u,  
                    const FloatingPointType& x) -> FloatingPointType  
  {  
    using std::pow;  
  
    // See also the third series representation provided in:  
    // https://functions.wolfram.com/HypergeometricFunctions/LegendreP2General/06/01/04/  
  
    const FloatingPointType u_half       = u / 2U;  
    const FloatingPointType one_minus_x  = 1U - x;  
    const FloatingPointType one_minus_mu = 1U - u;  
  
    const FloatingPointType h2f1_reg_term = ::math::wide_decimal::hypergeometric_2f1_regularized(-v,  
                                                                                                 1U + v,  
                                                                                                 one_minus_mu,  
                                                                                                 one_minus_x / 2U);  
  
    return (pow(1U + x, u_half) * h2f1_reg_term) / pow(one_minus_x, u_half);  
  }  
  
  template<typename FloatingPointType>  
  auto legendre_qvu(const FloatingPointType& v,  
                    const FloatingPointType& u, // NOLINT(bugprone-easily-swappable-parameters)  
                    const FloatingPointType& x) -> FloatingPointType  
  {  
    using std::cos;  
    using std::pow;  
    using std::sin;  
  
    // See also the third series representation provided in:  
    // https://functions.wolfram.com/HypergeometricFunctions/LegendreQ2General/06/01/02/  
  
    const FloatingPointType u_pi     = u * boost::math::constants::pi<FloatingPointType>();  
    const FloatingPointType sin_u_pi = sin(u_pi);  
    const FloatingPointType cos_u_pi = cos(u_pi);  
  
    const FloatingPointType one_minus_x          = 1U - x;  
    const FloatingPointType one_plus_x           = 1U + x;  
    const FloatingPointType u_half               = u / 2U;  
    const FloatingPointType one_minus_x_over_two = one_minus_x / 2U;  
  
    const FloatingPointType one_plus_x_over_one_minus_x_pow_u_half = pow(one_plus_x / one_minus_x, u_half);  
  
    const FloatingPointType v_plus_one =  v + 1U;  
  
    const FloatingPointType h2f1_1 = ::math::wide_decimal::hypergeometric_2f1_regularized(-v, v_plus_one, 1U - u, one_minus_x_over_two);  
    const FloatingPointType h2f1_2 = ::math::wide_decimal::hypergeometric_2f1_regularized(-v, v_plus_one, 1U + u, one_minus_x_over_two);  
  
    const FloatingPointType term1 = (h2f1_1 * one_plus_x_over_one_minus_x_pow_u_half) * cos_u_pi;  
    const FloatingPointType term2 = (h2f1_2 / one_plus_x_over_one_minus_x_pow_u_half) * ::math::wide_decimal::pochhammer(v_plus_one - u, u * 2U);  
  
    return (boost::math::constants::half_pi<FloatingPointType>() * (term1 - term2)) / sin_u_pi;  
  }  
} // namespace example009a_boost  
  
auto math::wide_decimal::example009a_boost_math_standalone() -> bool  
{  
  using std::fabs;  
  
  using example009a_boost::dec1001_t;  
  
  const dec1001_t x = dec1001_t(UINT32_C(789)) / 1000U;  
  
  // Compute some values of the Legendre function of the second kind  
  // on the real axis within the unit circle.  
  
  const dec1001_t lpvu = example009a_boost::legendre_pvu(dec1001_t(1U) / 3, dec1001_t(1U) / 7, x);  
  const dec1001_t lqvu = example009a_boost::legendre_qvu(dec1001_t(1U) / 3, dec1001_t(1U) / 7, x);  
  
  // N[LegendreP[1/3, 1/7, 2, 789/1000], 1001]  
  const dec1001_t control_lpvu  
  {  
    "0."  
    "9931591854934064572568089793337657296924109412673643417874724597677037521767383011114922218212896908"  
    "0027097864963936168565931417802571392659902755985572332367496479113814794086569608406498358078841079"  
    "6614332253952321909228583950735229742762075393962082193481956571473030793638066743365463314212686774"  
    "9705846436214183229248546560118160013959435929087793393518594318714950812356650788732887603501474515"  
    "6004025852431146299389135092485894348077166693965037523229349437595551471389905323765440198747406384"  
    "7110644718744990985775015222792213207021714039394865333620745229521299594761662471248344570118744500"  
    "7704200859337570117525726782130267734112267915875216713886079342015849430715707275265907079075801589"  
    "9475349854755219148506164974284035858053125225329876755631039303090095663330665771069643631805565017"  
    "9727332815465053842209475384208231035618687598506479237119775461739092129167925542731334863321783844"  
    "1556064262945029582348726229003376197479146725615623608519444682192209137686438989212000029759855669"  
    "1"  
  };  
  
  // N[LegendreQ[1/3, 1/7, 2, 789/1000], 1001]  
  const dec1001_t control_lqvu  
  {  
    "0."  
    "1802701358635473503357654947586116081212814896218637834466278197869512252395895222740695429982146035"  
    "6031050553694633844449903989916722532336371811084898600594152868857308967282179462211522993788162867"  
    "7937940705666514125775695969967978227378780279613276198008930643396707125474811188759254517278724120"  
    "7389289773410911722431603383521650557365445713405684637195534839239774206409352127340544908988632105"  
    "4776247480393326238840576035618210568727854433323584609583906187077896326821742487572480458213064013"  
    "1294415389805610364101254712548823884952831764415986558963073042187229383073285433144958849261339379"  
    "0888456281955232772521261719386944579027738990521656069899209895510911292249112861615412603542600625"  
    "5493560671059547162388837704126463700356368628825425175294509942750482619888824124287573395907950466"  
    "7777749042472348446167661381721631967592025204419250011417080752961739993679046744726634374246832558"  
    "5282958111218866577533906371773555762994157451873992840942126875958383079095536901373567200448533247"  
    "7"  
  };  
  
  const dec1001_t closeness_lpvu = fabs(1 - (lpvu / control_lpvu));  
  const dec1001_t closeness_lqvu = fabs(1 - (lqvu / control_lqvu));  
  
  const auto result_lpvu_is_ok = (closeness_lpvu < (std::numeric_limits<dec1001_t>::epsilon() * UINT32_C(1000000)));  
  const auto result_lqvu_is_ok = (closeness_lqvu < (std::numeric_limits<dec1001_t>::epsilon() * UINT32_C(1000000)));  
  
  const auto result_is_ok = (result_lpvu_is_ok && result_lqvu_is_ok);  
  
  return result_is_ok;  
}  
  
// TBD: Handle exception catching in example009a_boost_math_standalone at a later time.  
auto main() -> int // NOLINT(bugprone-exception-escape)  
{  
  const auto result_is_ok = math::wide_decimal::example009a_boost_math_standalone();  
  
  std::cout << "result_is_ok: " << std::boolalpha << result_is_ok << std::endl;  
}  
  
```

---

## Comment 8

> Username: ckormanyos  
> Created at: 2022-05-13 06:23:34 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1125698021  

Hi John (@jzmaddock), as mentioned briefly in #786, it seems like a sensible explanation is shaping up.  
  
At the moment, there are several place in Math where `BOOST_MATH_THROW_EXCEPTION(...)` is utilized directly. One such place is in [`bernoulli_details.hpp`](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/special_functions/detail/bernoulli_details.hpp#L191).  
  
The macro `BOOST_MATH_THROW_EXCEPTION(...)` goes directly [here](https://github.com/boostorg/math/blob/4d5cc6972ea5576d0c7fbb7caee5a70512dea1b5/include/boost/math/tools/throw_exception.hpp#L18) in the presence of `BOOST_MATH_STANDALONE`.  
  
This point then uses standard C++ `throw` mechanism, regardless of the presence of `BOOST_NO_EXCEPTIONS` or lack thereof.  
  
Several other places in Math use the `BOOST_MATH_THROW_EXCEPTION(...)` mechanism directly.

---

## Comment 9

> Username: jzmaddock  
> Created at: 2022-05-13 18:15:13 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1126314669  

The last commit to https://github.com/boostorg/math/pull/786 fixes your test case.  
  
That leaves:  
  
* error_handling.hpp: these are all guarded by BOOST_NO_EXCEPTIONS and fine.  
* bernoulli_details.hpp: as above, all uses guarded by BOOST_NO_EXCEPTIONS.  
* hypergeometric_pFq.hpp: these are related to forcing termination after a specified timeout, I don't have an easy fix this, suggest for now a different termination method is used if you really want no exceptions.  
* non_finite_number_facets.hpp: these are probably justified on the basis that this is what these iostream facets are specified to do in this situation.  
* owen's T: this one's a bug, I'll fix shortly.  
* tools/throw_exception.hpp: all intentional.  
* cstdfloat/* : These are all iotream related, so perhaps not an issue?

---

## Comment 10

> Username: ckormanyos  
> Created at: 2022-05-13 18:28:42 UTC  
> Updated at: 2022-05-13 18:30:10 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1126327638  

> last commit to #786 fixes your test case  
  
Yes. Many thanks John. Commit a6db2d19b7e96f802af751b039090d184343b3b5 is confirmed to fix my test case.  
  
Honestly, `error_handling.hpp` and `bernoulli_details.hpp` are the areas that I've never quite been able figure out --- until now, with your help and as the code base reached this level of clarity (standalone, C++11, etc.).  
  
I am really glad you helped with this.  
  
A lot of my work in exception-free environments uses a _very_ selective and subjective favorite part of Math (spec-fun usually involving some kinds of needs for `tgamma()`). So the first two areas you mentioned above will help me finish most of my benchmarking on the _metal_ and also help as the search continues for higher warning levels (assuming we expand this to parts of Math in following trimesters).  
  
This is really cool.

---

## Comment 11

> Username: ckormanyos  
> Created at: 2022-05-13 18:33:14 UTC  
> Updated at: 2022-05-13 18:33:47 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1126337142  

> `cstdfloat/*` : These are all `<iotream>`-related, so perhaps not an issue?  
  
I will not be benchmarking these in exception-free zones during the upcoming trimesters. So in my opinion, we can happily wait and cross that bridge when we come to it...

---

## Comment 12

> Username: jzmaddock  
> Created at: 2022-11-25 16:34:23 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1327682963  

@ckormanyos is this issue still active or basically fixed now?

---

## Comment 13

> Username: ckormanyos  
> Created at: 2022-11-25 17:01:37 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1327718208  

Hi @jzmaddock (John) we fixed this with great effort. I have some bare metal builds running in the CI of other projects and they work fine without exceptions.  
  
Let's please close this for now. If anything creeps up, I suspect it will be either nothing or a tiny patch which we could handle if/when arises.

---

## Comment 14

> Username: ckormanyos  
> Created at: 2022-11-25 20:18:09 UTC  
> Url: https://github.com/boostorg/math/issues/785#issuecomment-1327864454  

Thank you John! (@jzmaddock)
