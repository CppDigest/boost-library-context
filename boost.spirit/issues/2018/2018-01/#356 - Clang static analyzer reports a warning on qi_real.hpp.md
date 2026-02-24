# #356 - Clang static analyzer reports a warning on qi_real.hpp [Closed]

> Username: akrzemi1  
> Created at: 2018-01-29 09:17:24 UTC  
> Updated at: 2018-02-15 18:36:20 UTC  
> Closed at: 2018-02-15 18:36:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/356  

Clang static analyzer gives me the following warning.  
  
```c++  
In file included from include/boost/spirit/include/qi_real.hpp:16:  
In file included from include/boost/spirit/home/qi/numeric/real.hpp:21:  
include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:318:31: warning: Function call argument is an uninitialized value  
            traits::assign_to(traits::negate(neg, n), attr);  
                              ^~~~~~~~~~~~~~~~~~~~~~  
```  
  
I cannot tell if it is a false-positive or if this can be silenced.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2018-01-31 15:02:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-361958927  

Here is a full report:  
```  
1 warning generated.  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:318:31: warning: 2nd function call argument is an uninitialized value [clang-analyzer-core.CallAndMessage]  
            traits::assign_to(traits::negate(neg, n), attr);  
                              ^  
cpp/test_spirit/main.cpp:20:10: note: Calling 'my_parse'  
  return my_parse(str, qi::double_, val);  
         ^  
cpp/test_spirit/main.cpp:10:12: note: Calling 'parse'  
  bool r = qi::parse(it, str.end(), parser, val);  
           ^  
/include/boost/spirit/home/qi/parse.hpp:86:16: note: Calling 'any_real_parser::parse'  
        return compile<qi::domain>(expr).parse(first, last, context, unused, attr);  
               ^  
/include/boost/spirit/home/qi/numeric/real.hpp:168:20: note: Calling 'real_impl::parse'  
            return extract::parse(first, last, attr_, RealPolicies());  
                   ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:188:13: note: Taking false branch  
            if (first == last)  
            ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:197:13: note: 'n' declared without an initial value  
            T n;  
            ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:204:13: note: Taking false branch  
            if (!got_a_number)  
            ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:229:13: note: Taking true branch  
            if (p.parse_dot(first, last))  
            ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:234:17: note: Taking true branch  
                if (p.parse_frac_n(first, last, acc_n, frac_digits))  
                ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:270:13: note: Taking true branch  
            if (e_hit)  
            ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:275:17: note: Taking false branch  
                if (p.parse_exp_n(first, last, exp))  
                ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:288:21: note: Calling 'scale'  
                    traits::scale(-frac_digits, n, acc_n);  
                    ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:66:13: note: Assuming 'exp' is >= 0  
        if (exp >= 0)  
            ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:66:9: note: Taking true branch  
        if (exp >= 0)  
        ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:72:17: note: Calling 'integral_constant::operator bool'  
            if (is_floating_point<T>() && exp > max_exp)  
                ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:72:17: note: Returning from 'integral_constant::operator bool'  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:72:17: note: Left side of '&&' is true  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:72:43: note: Assuming 'exp' is > 'max_exp'  
            if (is_floating_point<T>() && exp > max_exp)  
                                          ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:72:13: note: Taking true branch  
            if (is_floating_point<T>() && exp > max_exp)  
            ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:288:21: note: Returning from 'scale'  
                    traits::scale(-frac_digits, n, acc_n);  
                    ^  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:318:31: note: 2nd function call argument is an uninitialized value  
            traits::assign_to(traits::negate(neg, n), attr);  
                              ^  
```  
  
My test program:  
```c++  
#include <boost/spirit/include/qi_real.hpp>  
#include <boost/spirit/home/qi/parser.hpp>  
  
namespace qi = boost::spirit::qi;  
  
template <typename P, typename T>  
bool my_parse(std::string const& str, P&& parser, T& val)  
{  
  auto it = str.begin();  
  bool r = qi::parse(it, str.end(), parser, val);  
  
  if (it != str.end()) return false;  
  return r;  
}  
  
int main()  
{  
  double val;  
  std::string str = "1." + std::string(309, '3') + "e";  
  return my_parse(str, qi::double_, val);  
}  
  
```

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-01-31 21:00:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-362068846  

Thanks for reporting this. Spirit real parsers are known to have bugs on some extreme numbers.  
This may be not a false positive, because `n` is initialized only if `traits::scale` returns true, but `traits::scale(-frac_digits, n, acc_n)` call is not checked.

---

## Comment 3

> Username: djowel  
> Created at: 2018-01-31 22:59:06 UTC  
> Updated at: 2018-01-31 23:04:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-362099803  

In this very particular particular case, n is initialized in traits::scale with args (-19, n, 13333333333333333333).  
  
real_impl.hpp:66:13: note: Assuming 'exp' is >= 0 is wrong. exp is -19.   
  
n = T(acc_n) / pow10<T>(-exp); // &n	double	1.3333333333333335  
  
Can we trigger a test case where exp is really >= 0  and (is_floating_point< T >() && exp > max_exp) ?

---

## Comment 4

> Username: djowel  
> Created at: 2018-01-31 23:00:26 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-362100085  

looks like a false positive to me, at least for this particular case.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2018-02-01 07:18:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-362179525  

If this should turn out to be a false positive, is it possible to put an `assert` in the code in `real_impl.hpp` indicating the impossible combination of values? This would silence the static analyzer.

---

## Comment 6

> Username: djowel  
> Created at: 2018-02-01 23:46:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-362439624  

> If this should turn out to be a false positive, is it possible to put an assert in the code in real_impl.hpp indicating the impossible combination of values? This would silence the static analyzer.  
  
It's possible. I'll see what I can do.

---

## Comment 7

> Username: Kojoley  
> Created at: 2018-02-14 13:12:14 UTC  
> Updated at: 2018-02-14 13:12:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-365602872  

> In this very particular particular case, n is initialized in traits::scale with args (-19, n, 13333333333333333333).  
>   
> real_impl.hpp:66:13: note: Assuming 'exp' is >= 0 is wrong. exp is -19.  
  
True.  
  
> Can we trigger a test case where exp is really >= 0 and (is_floating_point< T >() && exp > max_exp) ?  
  
```cpp  
#include <boost/spirit/include/qi_real.hpp>  
#include <boost/spirit/home/qi/parser.hpp>  
#include <boost/lexical_cast.hpp>  
  
namespace qi = boost::spirit::qi;  
  
template <typename P, typename T>  
bool my_parse(std::string const& str, P&& parser, T& val)  
{  
    auto it = str.begin();  
    bool r = qi::parse(it, str.end(), parser, val);  
  
    if (it != str.end()) return false;  
    return r;  
}  
  
int main()  
{  
    double val;  
    std::string str = "1e" + boost::lexical_cast<std::string>(std::numeric_limits<double>::max_exponent10 + 1);  
    return my_parse(str, qi::double_, val);  
}  
```  
  
> looks like a false positive to me, at least for this particular case.  
  
I agree, and for the case above, but crucial part is here  
  
```  
/include/boost/spirit/home/qi/numeric/detail/real_impl.hpp:275:17: note: Taking false branch  
                if (p.parse_exp_n(first, last, exp))  
                ^  
```  
  
The both cases above hits true branch here.

---

## Comment 8

> Username: Kojoley  
> Created at: 2018-02-14 13:27:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-365606584  

@akrzemi1 can you please check, if [such asserts](https://github.com/Kojoley/spirit/commit/35e29ec5b10c5931939c57536d4a2c77281580b3) will silence the false-positive warning?

---

## Comment 9

> Username: Kojoley  
> Created at: 2018-02-14 15:30:12 UTC  
> Updated at: 2018-02-14 15:33:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-365642864  

I feel like we should place `assert(frac_digits >= 0)` after calling `parse_frac_n` and it will solve the warning too.  
IIUC `frac_digits` is signed to avoid signed/unsigned conversions.

---

## Comment 10

> Username: djowel  
> Created at: 2018-02-14 22:06:06 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-365760584  

> I feel like we should place assert(frac_digits >= 0) after calling parse_frac_n and it will solve the warning too.  
> IIUC frac_digits is signed to avoid signed/unsigned conversions.  
  
Sounds good to me!

---

## Comment 11

> Username: akrzemi1  
> Created at: 2018-02-15 07:48:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-365848939  

Does this mean the report from the static analyzer is a false positive?

---

## Comment 12

> Username: djowel  
> Created at: 2018-02-15 08:49:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-365861119  

> Does this mean the report from the static analyzer is a false positive?  
  
Yes

---

## Comment 13

> Username: akrzemi1  
> Created at: 2018-02-15 08:54:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/356#issuecomment-365862221  

I confirm that after applying changes in https://github.com/boostorg/spirit/pull/358/commits/7ba702ff8f29552bd994a66376f051e633fd2679 the warning message goes away.
