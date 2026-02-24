# #842 - Karma: `real_generator` output for `long double` broken in Boost 1.90.0 [Closed]

> Username: sergiud  
> Created at: 2025-12-18 20:20:37 UTC  
> Updated at: 2025-12-20 21:48:43 UTC  
> Closed at: 2025-12-20 21:48:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/842  

After updating to Boost 1.90.0 my unit tests started failing. Looking into the problem revealed that Karma now generates incorrect output for certain `long double` values.  
  
The following MWE demonstrates the problem:  
```cpp  
#include <iostream>  
#include <iterator>  
#include <limits>  
  
#include <boost/spirit/home/karma/generate.hpp>  
#include <boost/spirit/home/karma/numeric/real.hpp>  
#include <boost/spirit/home/karma/numeric/real_policies.hpp>  
  
  
template<class T>  
struct max_precision_policy : boost::spirit::karma::real_policies<T> {  
    static constexpr unsigned precision(T /*unused*/) noexcept {  
        return std::numeric_limits<T>::max_digits10;  
    }  
};  
  
int main()  
{  
    boost::spirit::karma::real_generator<long double,  
                                         max_precision_policy<long double>>  
        real;  
  
    constexpr long double value = 1.55;  
    boost::spirit::karma::generate(std::ostreambuf_iterator{std::cout}, real,  
                                   value);  
    std::cout << std::endl;  
}  
```  
outputs the floating point value `1.55` as `1.0550000000000000044406` which is clearly incorrect. In Boost 1.89 the generated output is `1.550000000000000044406` as expected. The issue, however, is not limited to this specific value.  
  
Bisecting points to e6fbdf615b43fa7a03db4877079e572481a55f35 as the bad commit.

---

## Comment 1

> Username: djowel  
> Created at: 2025-12-19 02:11:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/842#issuecomment-3673164921  

@saki7 @sigbjorn Can you take a look please? Thanks!

---

## Comment 2

> Username: saki7  
> Created at: 2025-12-19 15:19:14 UTC  
> Updated at: 2025-12-19 15:21:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/842#issuecomment-3675464672  

To be honest, I'm entirely not sure why the new implementation outputs the wrong value. The new implementation introduced in e6fbdf615b43fa7a03db4877079e572481a55f35 actually did address the specific issue #792. It does contain the new unit test and it works just like intended.  
  
Sadly I'm not an expert of IEEE floating point format, so this problem goes beyond the scope of my expertise.  
  
Regarding Spirit.X4, I'm thinking of removing old floating point implementation and moving toward using `std::from_chars` if possible.

---

## Comment 3

> Username: sergiud  
> Created at: 2025-12-19 15:50:34 UTC  
> Updated at: 2025-12-19 15:50:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/842#issuecomment-3675564995  

The change to output in e6fbdf615b43fa7a03db4877079e572481a55f35, while addressed a reported issue, simultaneously introduced a regression. This means that the change was not entirely correct to begin with.  
  
The underlying issue here is that most real tests use `double` and disregard other floating point types.  
  
Anyway, I have a fix for the regression which I will submit shortly.

---

## Comment 4

> Username: djowel  
> Created at: 2025-12-20 00:48:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/842#issuecomment-3677100283  

> The change to output in [e6fbdf6](https://github.com/boostorg/spirit/commit/e6fbdf615b43fa7a03db4877079e572481a55f35), while addressed a reported issue, simultaneously introduced a regression. This means that the change was not entirely correct to begin with.  
>   
> The underlying issue here is that most real tests use `double` and disregard other floating point types.  
>   
> Anyway, I have a fix for the regression which I will submit shortly.  
  
That will be great! Thank you!
