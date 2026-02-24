# #529 - karma::real_policies<double>::fraction_part() formats incorrectly for precision >= 16 for fractions whose digits form an integer >= 999999999999998 [Closed]

> Username: jvanvuren-tableau  
> Created at: 2019-08-28 00:55:02 UTC  
> Updated at: 2019-09-19 14:16:36 UTC  
> Closed at: 2019-08-30 20:49:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/529  

For the current version of Boost, the Karma reals formatting library uses the following code from karma::real_policies<double>::fraction_part():  
  
        static bool fraction_part (OutputIterator& sink, T n  
          , unsigned precision_, unsigned precision)  
        {  
            ...  
            typename remove_const<T>::type digits =   
                (traits::test_zero(n) ? 0 : floor(log10(n))) + 1;  
            ...  
  
When the precision is >= 16, and "n" (the fractional part of the number converted to an integer) is >= 999999999999998, the expression floor(log10(n))+1 is returning a number of digits required to represent "n" that is 1 too large. When this happens, the final formatted double is 10 times the correct value.  
  
17 digits of precision is the max supported by doubles (https://en.wikipedia.org/wiki/Double-precision_floating-point_format), so it is likely that clients of the Karma library will try to configure it to use that precision, fufilling one of the preconditions for this issue to occur.  
  
Sample code:  
```  
#include <iostream>  
  
#include <boost/spirit/include/karma.hpp>  
  
namespace karma = boost::spirit::karma;  
  
struct double_policy : karma::real_policies<double> {  
  
    unsigned int precision(double n) const {  
        return 16;  
    }  
};  
  
int main() {  
  
    typedef karma::real_generator<double, double_policy> double_generator;  
    const double_generator double_gen = double_generator();  
  
    double x = 0.0999999999999998;  
    std::string generated;  
    std::back_insert_iterator<std::string> sink(generated);  
    karma::generate(sink, double_gen, x);  
  
    std::cout << generated << std::endl;  
  
    return 0;  
}  
```  
Output:  
0.999999999999998

---

## Comment 1

> Username: hkaiser  
> Created at: 2019-08-28 11:41:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/529#issuecomment-525706861  

@jvanvuren-tableau thanks for reporting this issue. Would you have any ideas on how to fix this?

---

## Comment 2

> Username: jvanvuren-tableau  
> Created at: 2019-08-28 14:56:38 UTC  
> Updated at: 2019-08-28 14:59:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/529#issuecomment-525783539  

I'm no expert in dealing with floating-point issues, but I am currently attempting to work around the issue by overriding karma::real_policies::fraction_part().   
  
In the override, I inline the code for karma::real_policies::fraction_part() and replace the call to "floor(log10(n))" with a call to a new method "intDigits(double n)" to calculate the number of digits in "n". See the code below.  
  
The new method "intDigits(double n)" iterates and multiplies by 10 to calculate the number of digits in "n". While this is producing correct results in my testing so far, I'm guessing this method is slower than "floor(log10(n))" when n is above a certain value.  
  
Perhaps the fix could be to replace the call to "floor(log10(n))" in karma::real_policies::fraction_part() with code that works like "initDigits(double n)". But the performance hit may be too much.  
  
Here is the code showing this approach:  
  
```  
#include <iostream>  
  
#include <boost/spirit/include/karma.hpp>  
  
namespace karma = boost::spirit::karma;  
  
  
struct double_policy : karma::real_policies<double> {  
  
    unsigned int precision(double n) const {  
        return 16;  
    }  
  
    // computes the number of digits left of the decimal in base 10  
    static unsigned int intDigits(double n) {  
        if (n < 0)  
            n = std::abs(n);  
  
        // need to do this in 64-bit to be able to represent large enough numbers  
        const uint64_t limit = UINT64_MAX / 10;  
        uint64_t num = floor(n);  
        for (uint64_t x = 10, i = 1;; x *= 10, i++) {  
            if (num < x)  
                return i;  
            if (x > limit)  
                return i + 1;  
        }  
    }  
  
    // Output the fractional part of the number  
    //  
    // By default, if the fractional part is zero we do nothing, unless the policy  
    // was explicitly told to do so  
    template <typename OutputIterator>  
    bool fraction_part(OutputIterator& sink, double n, unsigned int precision_, unsigned int precision) const  
    {  
        // Inline code for karma::real_policies<double>::fraction_part() and change it to use  
        // a method to calculate number of digits for n that doesn't overflow when n is very large.  
        unsigned int digits = boost::spirit::traits::test_zero(n) ? 1 : intDigits(n);  
          
        bool r = true;  
        for (/**/; r && digits < precision_; digits = digits + 1)  
            r = boost::spirit::karma::char_inserter<>::call(sink, '0');  
        if (precision && r)  
            r = boost::spirit::karma::int_inserter<10>::call(sink, n);  
        return r;  
    }  
  
};  
  
int main() {  
  
    typedef karma::real_generator<double, double_policy> double_generator;  
    const double_generator double_gen = double_generator();  
  
    double x = 0.0999999999999998;  
    std::string generated;  
    std::back_insert_iterator<std::string> sink(generated);  
    karma::generate(sink, double_gen, x);  
  
    std::cout << generated << std::endl;  
  
    return 0;  
}  
```  
  
  
Output:   
0.0999999999999998

---

## Comment 3

> Username: hkaiser  
> Created at: 2019-08-29 12:20:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/529#issuecomment-526159934  

@jvanvuren-tableau Thanks! This looks like a viable solution for larger `n`. Would you have an idea what this `n` could be? Also, would you mind creating a PR for this. I'd gladly accept it.

---

## Comment 4

> Username: jvanvuren-tableau  
> Created at: 2019-08-30 18:25:51 UTC  
> Url: https://github.com/boostorg/spirit/issues/529#issuecomment-526702173  

I've been doing some more reading. The double standard actually only supports 15.9 digits of precision - see https://en.wikipedia.org/wiki/Floating-point_arithmetic. Therefore for someone to try to use karma for format a double with a precision >= 16 digits is invalid. Which would mean that this issue which I submitted is actually invalid :)

---

## Comment 5

> Username: hkaiser  
> Created at: 2019-08-30 20:47:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/529#issuecomment-526744507  

Ok, that's what I was thinking... Are you ok with closing the issue?

---

## Comment 6

> Username: jvanvuren-tableau  
> Created at: 2019-08-30 20:49:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/529#issuecomment-526745039  

Yes - closing issue.

---

## Comment 7

> Username: jvanvuren-tableau  
> Created at: 2019-09-18 18:17:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/529#issuecomment-532804607  

Just a cautionary note - the situation where the log10() call in karma::real_policies<double>::fraction_part() returns a number one too large actually does happen when karma is configured for 15 digits of precision and the input "n" (the digits of the fractional part of the number converted to an integer, stored in a double) is 999999999999998 or larger. But because there are no leading zeroes to be inserted, this issue does not cause the function to incorrectly format the fractional part of the number.

---

## Comment 8

> Username: hkaiser  
> Created at: 2019-09-19 14:16:36 UTC  
> Url: https://github.com/boostorg/spirit/issues/529#issuecomment-533151221  

@jvanvuren-tableau Thanks for this clarification!
