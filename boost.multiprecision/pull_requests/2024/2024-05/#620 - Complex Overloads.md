# #620 Complex Overloads [Open]

> Username: mborland  
> Created at: 2024-05-06 10:40:17 UTC  
> Updated at: 2024-07-07 11:23:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620  

@NAThompson I started poking around on this after you asked. I found the only real way to have viable overloads for multiprecision types is to inject them into `namespace std`. The STLs use unconstrained templates for their functions unlike `<cmath>` From libc++:  
  
```  
template<class _Tp>  
inline _LIBCPP_INLINE_VISIBILITY  
_Tp  
abs(const complex<_Tp>& __c)  
{  
    return std::hypot(__c.real(), __c.imag());  
}  
```  
  
I also tried using the derived class (the giant commented out class) like:  
  
```  
namespace std {  
  
template <>  
class complex<boost::multiprecision::cpp_bin_float_50> : public boost::multiprecision::complex<boost::multiprecision::cpp_bin_float_50> {};  
```  
  
I don't think this is explicit illegal since the standard only specifies `T - the type of the real and imaginary parts. The behavior is unspecified (and may fail to compile) if T is not a cv-unqualified standard(until C++23) floating-point type and undefined if T is not NumericType`  
  
CC: @jzmaddock

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-06 11:13:56 UTC  
> Updated_at: 2024-05-17 14:21:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2095770773  

## [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/620?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
Attention: Patch coverage is `98.18653%` with `7 lines` in your changes are missing coverage. Please review.  
> Project coverage is 94.2%. Comparing base [(`7646f8e`)](https://app.codecov.io/gh/boostorg/multiprecision/commit/7646f8ec67c3be9579e3688726c32913e0de4bd2?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`ded45c0`)](https://app.codecov.io/gh/boostorg/multiprecision/pull/620?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
> :exclamation: **Current head ded45c0 differs from pull request most recent head 903d23c**  
>   
> Please [upload](https://docs.codecov.com/docs/codecov-uploader) reports for the commit 903d23c to get more accurate results.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/multiprecision/pull/620/graphs/tree.svg?width=650&height=150&src=pr&token=SDDBym7Pc9&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/multiprecision/pull/620?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@            Coverage Diff            @@  
##           develop    #620     +/-   ##  
=========================================  
+ Coverage     94.1%   94.2%   +0.1%       
=========================================  
  Files          274     276      +2       
  Lines        26766   27152    +386       
=========================================  
+ Hits         25163   25553    +390       
+ Misses        1603    1599      -4       
```  
  
  
| [Files](https://app.codecov.io/gh/boostorg/multiprecision/pull/620?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [test/test\_complex\_class.cpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/620?src=pr&el=tree&filepath=test%2Ftest_complex_class.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2NvbXBsZXhfY2xhc3MuY3Bw) | `100.0% <100.0%> (ø)` | |  
| [include/boost/multiprecision/complex.hpp](https://app.codecov.io/gh/boostorg/multiprecision/pull/620?src=pr&el=tree&filepath=include%2Fboost%2Fmultiprecision%2Fcomplex.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tdWx0aXByZWNpc2lvbi9jb21wbGV4LmhwcA==) | `90.3% <90.3%> (ø)` | |  
  
... and [3 files with indirect coverage changes](https://app.codecov.io/gh/boostorg/multiprecision/pull/620/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/multiprecision/pull/620?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/multiprecision/pull/620?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [7646f8e...903d23c](https://app.codecov.io/gh/boostorg/multiprecision/pull/620?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-05-06 18:08:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2096623952  

I suspect we get into trouble whatever here: about the only thing we're legally allowed to do is [partially-] specialise `std::complex` for type `number`.  The non member operators I *think* we can put in an "associated" namespace ie `boost::multiprecision` and ADL will still find them.  What we're not allowed to do, is add overloads for say `std::real` in `namespace std`.  I have a hunch potential bear traps lurk if we do that, but I don't see any good alternative.  
  
Implementation wise, since it's all already written (albeit under another name), I would just make `std::complex<boost::multiprecision::number<T, ET>>` a thin wrapper around our existing types (multiprecision does have an internal traits class for that somewhere - in order to implement `polar`).  That would then preserve things like the mpc wrapper where the backend can do complex arithmetic better than we can.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2024-05-06 20:27:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2096848626  

Is there sense in trying to upstream some of this to clang? It appears that they simply haven't imagined this usecase.

---

## Comment 4

> Username: mborland  
> Created_at: 2024-05-07 06:25:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2097539998  

> Is there sense in trying to upstream some of this to clang? It appears that they simply haven't imagined this usecase.  
  
I don't think so. Up to C++23 the standard specified standard floating point types, and since then it says trivially copyable literal numeric types. I assume they changed the wording to fit the types of `<stdfloat>`. I don't believe most of the multiprecision types (except maybe float128) meet that requirement.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2024-05-07 07:35:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2097643017  

It's a long standing C++ library issue: and tightened up in C++23, so for example MSVC will now static_assert if you try and use `std::complex` with anything other than the mandated types.

---

## Comment 6

> Username: mborland  
> Created_at: 2024-05-07 07:58:59 UTC  
> Updated_at: 2024-05-07 12:06:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2097684452  

> I suspect we get into trouble whatever here: about the only thing we're legally allowed to do is [partially-] specialise `std::complex` for type `number`. The non member operators I _think_ we can put in an "associated" namespace ie `boost::multiprecision` and ADL will still find them. What we're not allowed to do, is add overloads for say `std::real` in `namespace std`. I have a hunch potential bear traps lurk if we do that, but I don't see any good alternative.  
>   
> Implementation wise, since it's all already written (albeit under another name), I would just make `std::complex<boost::multiprecision::number<T, ET>>` a thin wrapper around our existing types (multiprecision does have an internal traits class for that somewhere - in order to implement `polar`). That would then preserve things like the mpc wrapper where the backend can do complex arithmetic better than we can.  
  
This works locally. Added in: https://github.com/boostorg/multiprecision/pull/620/commits/f61a9e1a976d21da39e689144e3491a818b615a1. How would you use the existing backend implementation of polar instead of the one provided? Simple enable_if in the event we know the type has that functionallity?  
  
edit: Figured it out here: https://github.com/boostorg/multiprecision/pull/620/commits/612f4fe2dfbfd314bf734d212320631b83786b48

---

## Review 7 [Commented]

> Username: jzmaddock  
> Created_at: 2024-05-08 16:47:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/620#pullrequestreview-2046160178  

📁 include/boost/multiprecision/complex.hpp

```diff
 297 |+ 
 298 |+ template <typename T, expression_template_option ET>
 299 |+ inline BOOST_MP_CXX14_CONSTEXPR complex<boost::multiprecision::number<T, ET>> exp(const complex<boost::multiprecision::number<T, ET>>& z) noexcept
```

> Username: jzmaddock  
> Created_at: 2024-05-08 16:47:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#discussion_r1594329268  

@mborland you're re-inventing the wheel here?  All these complex number functions are already implemented, it's just that `number<whatever>` is _already_ a complex number type with the same interface as `std::complex` for a suitably defined `whatever`.  See https://www.boost.org/doc/libs/1_85_0/libs/multiprecision/doc/html/boost_multiprecision/tut/complex.html

> Username: mborland  
> Created_at: 2024-05-08 17:01:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#discussion_r1594345248  

In @NAThompson's original issue he was using `cpp_bin_float_100` as the number type. The templates could be `enable_if` for the already complex types, but if we want to allow a wrapper of any floating-point multiprecision type I don't see any other way around this.

> Username: jzmaddock  
> Created_at: 2024-05-08 17:23:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#discussion_r1594376198  

I guess my point is that adding a `boost::multiprecision::complex` class duplicates what is already there and adds confusion (and maintenance), what if you had (caution untested code ahead!!):  
  
```  
template <typename T, boost::multiprecision::expression_template_option ET>  
class complex<boost::multiprecision::number<T, ET>>  
{  
  using self_type = complex<boost::multiprecision::number<T, ET>>;  
  using complex_type = decltype(polar(std::declval<boost::multiprecision::number<T, ET>>(), std::declval<boost::multiprecision::number<T, ET>>()));  
  complex_type m_data;  
  
  complex_type& data(){ return m_data; }  
  const complex_type& data()const{ return m_data; }  
public:  
  // public members here..  
  
  // and then:  
  friend inline self_type operator+(const self_type& a, const self_type& b)  
  { return a.data() + b.data(); }  
  // etc  
  friend inline self_type exp(const self_type& val)   
  { return exp(val.data()); }  
  // etc  
};  
```  
  
Now there's no duplication, so bugs fixed in one place are fixed everywhere.  Plus if the user has included mpc.hpp for example then mpc will be used for mpfr complex number types rather than synthesising our own type.  
  
It's still a bind writing out all the forwarding functions, but at least they're trivial and improvements in one place propagate throughout.

> Username: mborland  
> Created_at: 2024-05-08 17:36:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#discussion_r1594395271  

I can give that a go tomorrow. Forwarding functions will be monotonous which is fine. What is non-obvious to me is how to overload say self_type + complex type like is here: https://github.com/boostorg/multiprecision/pull/620/files#diff-db4d91a1fc843b60d38c832c49b4b5ec9b8c22f772d5c5d0a56eea563ae59a5dR50. I'll play with it

> Username: jzmaddock  
> Created_at: 2024-05-08 17:58:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#discussion_r1594431071  

I think you have the form of the function already there?  
  
BTW those functions are definitely NOT noexcept in the multiprecision case: memory allocations usually are involved!


---

## Comment 8

> Username: mborland  
> Created_at: 2024-05-09 08:57:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2102248159  

@jzmaddock is https://github.com/boostorg/multiprecision/pull/620/commits/9b3575b34522281283d6df3499e458fb02ae210e more what you were expecting? The only thing I don't see a way around is overloading `polar` to return `std::complex`. The existing overload in default ops takes two `number` types so the only difference between the two would be the return type which is not allowed.

---

## Comment 9

> Username: jzmaddock  
> Created_at: 2024-05-12 10:35:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2106200732  

Apologies Matt, have been travelling, will try and look at this tomorrow...

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2024-05-14 11:33:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2109971605  

> @jzmaddock is https://github.com/boostorg/multiprecision/commit/9b3575b34522281283d6df3499e458fb02ae210e more what you were expecting?   
  
Yup, thanks!  
  
>The only thing I don't see a way around is overloading polar to return std::complex. The existing overload in default ops takes two number types so the only difference between the two would be the return type which is not allowed.  
  
Oh :(  Are they not OK as long as they are in different namespaces?

---

## Comment 11

> Username: mborland  
> Created_at: 2024-05-14 11:56:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2110014515  

> Oh :( Are they not OK as long as they are in different namespaces?  
  
Interestingly you would think it would be fine. If I move all the new stuff into `boost::multiprecision::complex` and then add `using namespace boost::multiprecision::complex` to the test file I start getting ADL errors where multiprecision types fail during substituion into the STL functions. Maybe I am missing something?

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2024-05-14 18:11:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2110831663  

> Interestingly you would think it would be fine. If I move all the new stuff into boost::multiprecision::complex and then add using namespace boost::multiprecision::complex to the test file I start getting ADL errors where multiprecision types fail during substituion into the STL functions. Maybe I am missing something?  
  
The only solution I could find for now, was to place polar in namespace std, and make all calls to polar explicit: ADL will never work here, because the multiprecision overload will always be found via ADL, and that's the overload we don't want!  
  
Tentatively pushed.

---

## Comment 13

> Username: mborland  
> Created_at: 2024-05-17 12:24:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2117484659  

@jzmaddock since this directly overloads `std::complex` do we need to add a specialization of Eigen::NumTraits beyond what you have here: https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/eigen.hpp

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2024-05-17 12:42:04 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2117517146  

> since this directly overloads std::complex do we need to add a specialization of Eigen::NumTraits beyond what you have here  
  
I don't _think_ so but could be wrong, has this been tried in Nick's original problem?

---

## Comment 15

> Username: NAThompson  
> Created_at: 2024-05-18 02:26:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2118604354  

> I don't think so but could be wrong, has this been tried in Nick's original problem?  
  
@mborland: The polynomial roots code is pretty trivial; want to just merge my branch into this one and close the other PR?

---

## Comment 16

> Username: mborland  
> Created_at: 2024-05-18 06:51:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2118667703  

> > I don't think so but could be wrong, has this been tried in Nick's original problem?  
>   
> @mborland: The polynomial roots code is pretty trivial; want to just merge my branch into this one and close the other PR?  
  
Does it belong in multi-precision since right now the PR is against math. I added the test set from your comment in the most recent two commits.

---

## Comment 17

> Username: NAThompson  
> Created_at: 2024-05-18 15:41:59 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2118861630  

@mborland : Yup, you're right . . . that was a silly comment earlier.

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2024-07-07 11:15:20 UTC  
> Updated_at: 2024-07-07 11:17:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2212412705  

Hi Matt (@mborland) I just ran this thing through some local implementations I have of the complex-valued Riemann-Zeta function.  
  
Just a heads-up I think the complex-adaption is missing overloads for  $x^n$, where $x$ is multiprecision and real-valued, yet $n$ is either `int` or possibly a signed or unsigned integral type (where this means integral in the sense of `std::is_integral`).  
  
MSVC when doing those integral pawers ended up calling `std::pow` and kicking out the multiple-precision. Whereby GCC's implemenation of `<complex>` somewhow got it right. Nonetheless, I think actually present clear overloads are needed for complex-to-power-of-integral.  
  
Cc: @jzmaddock

---

## Comment 19

> Username: ckormanyos  
> Created_at: 2024-07-07 11:23:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/620#issuecomment-2212414971  

The so-called _ladder_ _method_ comes to mind, which I have appended from my own, self-written `extended_complex` class years ago.  
  
```cpp  
    template<typename T, typename EnableType> complex<T, EnableType> pow(const complex<T, EnableType>& my_z, int my_pn)  
    {  
      if     (my_pn <  static_cast<int>(INT8_C(0))) { return  T(static_cast<unsigned>(UINT8_C(1))) / pow(my_z, -my_pn); }  
      else if(my_pn == static_cast<int>(INT8_C(0))) { return  complex<T, EnableType>(T(static_cast<unsigned>(UINT8_C(1)))); }  
      else if(my_pn == static_cast<int>(INT8_C(1))) { return  my_z; }  
      else if(my_pn == static_cast<int>(INT8_C(2))) { return  my_z * my_z; }  
      else if(my_pn == static_cast<int>(INT8_C(3))) { return (my_z * my_z) * my_z; }  
      else if(my_pn == static_cast<int>(INT8_C(4))) { const complex<T, EnableType> my_z2(my_z * my_z); return (my_z2 * my_z2); }  
      else  
      {  
        complex<T, EnableType> my_result = T(static_cast<unsigned>(UINT8_C(1)));  
  
        complex<T, EnableType> y(my_z);  
  
        auto p_local = static_cast<std::uint64_t>(my_pn);  
  
        // Use the so-called ladder method for the power calculation.  
        for(;;)  
        {  
          const auto lo_bit = static_cast<std::uint_fast8_t>(p_local & static_cast<unsigned>(UINT8_C(1)));  
  
          const auto do_power_multiply = (lo_bit != static_cast<std::uint_fast8_t>(UINT8_C(0)));  
  
          if(do_power_multiply)  
          {  
            my_result *= y;  
          }  
  
          p_local >>= static_cast<unsigned>(UINT8_C(1));  
  
          if(p_local == static_cast<std::uint64_t>(UINT8_C(0)))  
          {  
            break;  
          }  
  
          y *= y;  
        }  
  
        return my_result;  
      }  
    }  
```  
  
Cc: @mborland and @jzmaddock

---
