# #1131 Polynomial roots via eigenvalues of the companion matrix [Open]

> Username: NAThompson  
> Created at: 2024-05-04 16:58:54 UTC  
> Updated at: 2024-10-15 07:57:58 UTC  
> Url: https://github.com/boostorg/math/pull/1131  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2024-05-04 17:01:43 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2094302905  

@jzmaddock : I did test it!  
  
@mborland , @jzmaddock : Quick question: Given a floating point type `T`, how to I form the associated complex type in the multiprecision case? To wit, `std::complex<T>` will fail for `float128`, `cpp_bin_float_100`, etc.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2024-05-04 17:53:40 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2094326485  

>Quick question: Given a floating point type T, how to I form the associated complex type in the multiprecision case? To wit, std::complex<T> will fail for float128, cpp_bin_float_100, etc.  
  
Ugh, seems to be a bridge we haven't crossed yet.    
  
However, `typeof(polar(T(), T())`  would do it, since we never use expression templates for that.  
  
Note that in order to use multiprecision types with Eigen we need to include <boost/multiprecision/eigen.hpp> which specializes a bunch of Eigen traits classes.  I guess to avoid nasty surprises this header should include that when available, though it adds an often not needed dependency :(  
  
The static_assert on is_floating_point would have to go as well...

---

## Comment 3

> Username: NAThompson  
> Created_at: 2024-05-04 18:18:17 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2094337256  

> The static_assert on is_floating_point would have to go as well...  
  
Meh, who cares. Got rid of it.  
  
> Note that in order to use multiprecision types with Eigen we need to include <boost/multiprecision/eigen.hpp> which specializes a bunch of Eigen traits classes. I guess to avoid nasty surprises this header should include that when available, though it adds an often not needed dependency :(  
  
I did the following:  
  
```cpp  
#if __has_include(<Eigen/Eigenvalues>)  
#include <Eigen/Eigenvalues>  
#include <complex> // roots are complex numbers.  
   #if __has_include(<boost/multiprecision/eigen.hpp>)  
   #include <boost/multiprecision/eigen.hpp>  
   #include <boost/math/cstdfloat/cstdfloat_complex_std.hpp>  
   #endif  
#endif  
```  
  
I don't feel too bad about this . . .  
  
> However, typeof(polar(T(), T()) would do it, since we never use expression templates for that.  
  
Suffering just a bit on this one:  
  
```  
In file included from test/polynomial_roots_test.cpp:16:  
In file included from ./include/boost/math/tools/polynomial.hpp:38:  
./include/boost/math/cstdfloat/cstdfloat_complex_std.hpp:31:11: fatal error: reference to 'complex' is ambiguous  
   31 |     class complex<BOOST_CSTDFLOAT_EXTENDED_COMPLEX_FLOAT_TYPE>;  
      |           ^  
./include/boost/math/cstdfloat/cstdfloat_complex_std.hpp:28:11: note: candidate found by name lookup is 'std::complex'  
   28 |     class complex;  
      |           ^  
/opt/homebrew/opt/llvm/bin/../include/c++/v1/complex:260:28: note: candidate found by name lookup is 'std::__1::complex'  
  260 | class _LIBCPP_TEMPLATE_VIS complex {  
```

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2024-05-04 18:27:17 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2094339667  

> #include <boost/math/cstdfloat/cstdfloat_complex_std.hpp>  
  
We really shouldn't need that, and it might get rid of the error?  
  
And of course I should have said `decltype` since `typeof` is rather last century (like me!!)  :)

---

## Comment 5

> Username: NAThompson  
> Created_at: 2024-05-04 18:52:10 UTC  
> Updated_at: 2024-05-04 19:00:49 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2094345931  

> We really shouldn't need that, and it might get rid of the error?  
  
Got rid of it; now I'm here:  
  
```  
In file included from test/polynomial_roots_test.cpp:12:  
/opt/homebrew/opt/llvm/bin/../include/c++/v1/complex:730:62: fatal error: no matching function for call to '__constexpr_fabs'  
  730 |   _Tp __logbw  = std::__constexpr_logb(std::__constexpr_fmax(std::__constexpr_fabs(__c), std::__constexpr_fabs(__d)));  
      |                                                              ^~~~~~~~~~~~~~~~~~~~~  
/opt/homebrew/Cellar/eigen/3.4.0_1/include/eigen3/Eigen/src/Eigenvalues/EigenSolver.h:542:74: note: in instantiation of function template specialization 'std::operator/<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<100>>>' requested here  
  542 |         ComplexScalar cc = ComplexScalar(Scalar(0),-m_matT.coeff(n-1,n)) / ComplexScalar(m_matT.coeff(n-1,n-1)-p,q);  
```  
  
Looks like this is a pure Eigen/Multiprecision interop issue? For example, this:  
  
```cpp  
#include <Eigen/Dense>  
#include <Eigen/Eigenvalues>  
#include <boost/multiprecision/eigen.hpp>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
using boost::multiprecision::cpp_bin_float_100;  
int main() {  
  
    Eigen::Matrix<cpp_bin_float_100, Eigen::Dynamic, Eigen::Dynamic> A = Eigen::Matrix<cpp_bin_float_100, Eigen::Dynamic, Eigen::Dynamic>::Identity(3,3);  
    Eigen::EigenSolver<decltype(A)> es;  
    es.compute(A, /*computeEigenvectors=*/ false);  
  
    auto eigs = es.eigenvalues();  
    for (auto eig : eigs) {  
      std::cout << eig << "\n";  
    }  
}  
```  
  
yields the same error:  
  
```  
In file included from /opt/homebrew/Cellar/eigen/3.4.0_1/include/eigen3/Eigen/Dense:1:  
In file included from /opt/homebrew/Cellar/eigen/3.4.0_1/include/eigen3/Eigen/Core:50:  
/opt/homebrew/opt/llvm/bin/../include/c++/v1/complex:730:62: fatal error: no matching function for call to '__constexpr_fabs'  
  730 |   _Tp __logbw  = std::__constexpr_logb(std::__constexpr_fmax(std::__constexpr_fabs(__c), std::__constexpr_fabs(__d)));  
      |                                                              ^~~~~~~~~~~~~~~~~~~~~  
/opt/homebrew/Cellar/eigen/3.4.0_1/include/eigen3/Eigen/src/Eigenvalues/EigenSolver.h:542:74: note: in instantiation of function template specialization 'std::operator/<boost::multiprecision::number<boost::multiprecision::backends::cpp_bin_float<100>>>' requested here  
  542 |         ComplexScalar cc = ComplexScalar(Scalar(0),-m_matT.coeff(n-1,n)) / ComplexScalar(m_matT.coeff(n-1,n-1)-p,q);  
      |  
```  
  
Naively I feel like I should submit this to Eigen and see how much they want to prioritize multiprecision integration.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2024-05-04 19:13:57 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2094351300  

Ok, upon further investigation is appears that Eigen has the following lines of code everywhere:  
  
```cpp  
typedef std::complex<RealScalar> ComplexScalar;  
```  
  
I think we could probably convince them to do:  
  
```cpp  
using std::complex;  
typedef complex<RealScalar> ComplexScalar;  
```  
  
but I doubt we could get them to agree to   
  
```cpp  
using std::complex;  
using std::polar;  
typedef decltype(polar(RealScalar(), RealScalar()) ComplexScalar;  
```  
  
So we may have to construct the bridge before we can get multiprecision integration.

---

## Comment 7

> Username: codecov[bot]  
> Created_at: 2024-05-04 20:46:26 UTC  
> Updated_at: 2024-10-15 07:57:58 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2094374892  

## [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1131?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 93.69%. Comparing base [(`a53b013`)](https://app.codecov.io/gh/boostorg/math/commit/a53b013c735caa98179532a32ad24d34569b9710?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`04bd67a`)](https://app.codecov.io/gh/boostorg/math/commit/04bd67a0f7c240118fb7426fc8b8c785a068576b?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 471 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
[![Impacted file tree graph](https://app.codecov.io/gh/boostorg/math/pull/1131/graphs/tree.svg?width=650&height=150&src=pr&token=26IDb1OAa7&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)](https://app.codecov.io/gh/boostorg/math/pull/1131?src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1131      +/-   ##  
===========================================  
- Coverage    93.71%   93.69%   -0.02%       
===========================================  
  Files          771      771                
  Lines        61105    61105                
===========================================  
- Hits         57264    57254      -10       
- Misses        3841     3851      +10       
```  
  
| [Files with missing lines](https://app.codecov.io/gh/boostorg/math/pull/1131?dropdown=coverage&src=pr&el=tree&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) | Coverage Δ | |  
|---|---|---|  
| [include/boost/math/tools/polynomial.hpp](https://app.codecov.io/gh/boostorg/math/pull/1131?src=pr&el=tree&filepath=include%2Fboost%2Fmath%2Ftools%2Fpolynomial.hpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-aW5jbHVkZS9ib29zdC9tYXRoL3Rvb2xzL3BvbHlub21pYWwuaHBw) | `99.27% <ø> (ø)` | |  
| [test/test\_autodiff\_5.cpp](https://app.codecov.io/gh/boostorg/math/pull/1131?src=pr&el=tree&filepath=test%2Ftest_autodiff_5.cpp&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg#diff-dGVzdC90ZXN0X2F1dG9kaWZmXzUuY3Bw) | `100.00% <ø> (ø)` | |  
  
... and [1 file with indirect coverage changes](https://app.codecov.io/gh/boostorg/math/pull/1131/indirect-changes?src=pr&el=tree-more&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
  
------  
  
[Continue to review full report in Codecov by Sentry](https://app.codecov.io/gh/boostorg/math/pull/1131?dropdown=coverage&src=pr&el=continue&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://app.codecov.io/gh/boostorg/math/pull/1131?dropdown=coverage&src=pr&el=footer&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Last update [a53b013...04bd67a](https://app.codecov.io/gh/boostorg/math/pull/1131?dropdown=coverage&src=pr&el=lastupdated&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
  
</details>

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2024-05-05 10:44:24 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2094753154  

Well, I was going to say we should file a bug report again Eigen... and tried but gitlab tied me up in circles and I gave up :(

---

## Comment 9

> Username: NAThompson  
> Created_at: 2024-05-05 15:56:49 UTC  
> Updated_at: 2024-05-05 16:15:11 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2094859640  

> Well, I was going to say we should file a bug report again Eigen... and tried but gitlab tied me up in circles and I gave up :(  
  
Done: https://gitlab.com/libeigen/eigen/-/issues/2818; I do have some worry that they'll set it to WONTFIX until we have some canonical way of providing `complex<T>` for multiprecision `T`, but maybe they know some tricks.

---

## Comment 10

> Username: cantonios  
> Created_at: 2024-05-07 21:47:02 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2099365544  

> > Well, I was going to say we should file a bug report again Eigen... and tried but gitlab tied me up in circles and I gave up :(  
>   
> Done: https://gitlab.com/libeigen/eigen/-/issues/2818; I do have some worry that they'll set it to WONTFIX until we have some canonical way of providing `complex<T>` for multiprecision `T`,  
  
We won't WONTFIX ;).  But you'll need to provide the merge request.  
  
>  but maybe they know some tricks.  
  
Provided a suggestion on the Eigen bug.  Not really a "trick", but we have some traits you can use.  
  
Otherwise, you could explicitly specialize `std::complex<your_custom_type>` - that should be _legal_ as long as your type satisfies the [`NumericType`](https://en.cppreference.com/w/cpp/named_req/NumericType) requirement.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2024-05-08 08:21:02 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2100019915  

Thanks @cantonios for the prompt reply.  
  
> Otherwise, you could explicitly specialize std::complex<your_custom_type> - that should be legal as long as your type satisfies the [NumericType](https://en.cppreference.com/w/cpp/named_req/NumericType) requirement.  
  
We're looking at that now, as you say specializing `complex` appears to be legal, however overloading all the non-member functions (in namespace std) is definitely NOT legal and may well have unintended consequences, but I don't see a better way for now.

---

## Comment 12

> Username: cantonios  
> Created_at: 2024-05-08 15:32:52 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2100854455  

> however overloading all the non-member functions (in namespace std) is definitely NOT legal  
  
Why do you need to?  You should be able to rig it up so that non-members are found via ADL.  e.g. https://godbolt.org/z/fxTb8c8cf  
  
> but I don't see a better way for now.  
  
The better way (at least for Eigen) is to add an `Eigen::NumTraits<...>::Complex` member and use that.

---

## Comment 13

> Username: jzmaddock  
> Created_at: 2024-05-08 16:43:16 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2100987426  

> Why do you need to? You should be able to rig it up so that non-members are found via ADL.  
  
For sure, those overloads can be found via ADL, my expectation is that most users using `std::complex`, will be calling `std::exp`.  Perhaps I'm wrong?  
  
> The better way (at least for Eigen) is to add an Eigen::NumTraits<...>::Complex member and use that.  
  
Yes, that's what we were hoping for ;)

---

## Comment 14

> Username: cantonios  
> Created_at: 2024-05-08 17:03:39 UTC  
> Url: https://github.com/boostorg/math/pull/1131#issuecomment-2101024836  

> For sure, those overloads can be found via ADL, my expectation is that most users using `std::complex`, will be calling `std::exp`. Perhaps I'm wrong?  
  
Yes, this is usually true, but arguably a bug on the users' end if they expect it to work with custom types.  Besides, you still have this issue if you _don't_ specialize `std::complex`, opting for your own custom `CustomComplex` implementation, whatever `decltype(polar(...))` is.

---
