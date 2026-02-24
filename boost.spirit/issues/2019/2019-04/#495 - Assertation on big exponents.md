# #495 - Assertation on big exponents [Closed]

> Username: RandomInEqualities  
> Created at: 2019-04-23 16:02:03 UTC  
> Updated at: 2019-04-24 09:36:34 UTC  
> Closed at: 2019-04-24 09:36:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/495  

The following code:  
  
```c++  
#include <boost/spirit/home/x3.hpp>  
  
#include <string>  
  
namespace x3 = boost::spirit::x3;  
  
int main() {  
  const std::string expression = "1e310";  
  auto start = expression.cbegin();  
  auto end = expression.cend();  
  double result;  
  x3::phrase_parse(start, end, x3::double_, x3::space, result);  
  return 0;  
}  
```  
  
Asserts in pow10.hpp:  
  
```  
Assertion failed: dim < sizeof(exponents)/sizeof(double), file boost/spirit/home/x3/support/numeric_utils/pow10.hpp, line 85  
```  
  
Due to the exponentiation table only holding 308 values. In qi the expression is rejected.  
  
One can use their own real policy to workaround it:  
  
```c++  
template<typename T>  
struct RealPolicy : x3::ureal_policies<T> {  
    template<typename Iterator>  
    static bool parse_exp_n(Iterator& first, Iterator const& last, int& attr_) {  
        // boost spirit asserts/reads out of bounds when |exponent| is larger than 308  
        const Iterator save = first;  
        if (x3::ureal_policies<T>::parse_exp_n(first, last, attr_)) {  
            if (attr_ >= -308 && attr_ <= 308) {  
                return true;  
            } else {  
                first = save;  
                return false;  
            }  
        }  
        return false;  
    }  
};  
x3::real_parser<double, RealPolicy<double>> const double_ = {};  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-04-23 16:59:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/495#issuecomment-485888633  

That's a pretty old thing (https://svn.boost.org/trac10/ticket/9400), there are various corner cases in exponents handling...

---

## Comment 2

> Username: djowel  
> Created at: 2019-04-23 22:35:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/495#issuecomment-485999553  

This has been fixed in Qi in real_impl.hpp. The best strategy is to unify the code in one place. I'll see what I can do. What are the other corner cases, @Kojoley ?

---

## Comment 3

> Username: djowel  
> Created at: 2019-04-23 23:18:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/495#issuecomment-486008642  

@Kojoley, I'll take this one. Please tell me what other corner cases you are referring to.

---

## Comment 4

> Username: djowel  
> Created at: 2019-04-24 01:14:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/495#issuecomment-486029652  

@RandomInEqualities Fix is in develop.

---

## Comment 5

> Username: RandomInEqualities  
> Created at: 2019-04-24 06:33:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/495#issuecomment-486087421  

Thanks, I tested the patch locally, it works well for me.  
  
There is one regression, but I don't really care about that, but it is that "2.1111111e-303" cannot be parsed anymore due to the (exp-frac) in `scale(int exp, int frac, T& n)`, while it looks like scale(int exp, T& n) has some support for exponents lower than min_exp, so it could be parsed before. (But I think just rejecting is totally fine, e.g one would have to do 2.11111...111e-5 with 303 ones to see it in practice).

---

## Comment 6

> Username: djowel  
> Created at: 2019-04-24 06:36:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/495#issuecomment-486088052  

Good you checked. I'll have a look later. Should be easy. Thanks!

---

## Comment 7

> Username: djowel  
> Created at: 2019-04-24 08:02:00 UTC  
> Url: https://github.com/boostorg/spirit/issues/495#issuecomment-486114103  

Fixed for big negative exponents. Please try again before I close this.

---

## Comment 8

> Username: RandomInEqualities  
> Created at: 2019-04-24 09:36:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/495#issuecomment-486146247  

Yes, again thanks a lot. It works great now.
