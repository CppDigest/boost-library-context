# #792 - karma::real_policies<double>::fraction_part() formats incorrectly by order of magnitude for numbers like 1.09e-6, emits 1.9e-06(!) [Closed]

> Username: sigbjorn  
> Created at: 2024-09-19 00:23:33 UTC  
> Updated at: 2025-05-09 07:46:27 UTC  
> Closed at: 2025-05-09 07:43:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/792  

# Overview  
  
As detected reported on  Shyft open source, https://gitlab.com/shyft-os/shyft/-/issues/1216,  
  
**Wrong outputs** are generated for numbers of the form **1.09e-6**,  instead of generating expected '1.09e-06',  
it outputs **1.9e-06**  
-lacking trailing zeros before the 9, so an entirely different number, not resembling the input (like rounding errors etc).  
  
This is due to an edge case not detected in the test-setup, where the passed n to the fraction routine is of the form:  
  
9999.00000005 or similar.  
  
  
## How to reproduce  
  
Adding the following test snippet to the test-suite, karma/real3.cpp:  
  
```  
    // test for off by a magnitude due to log10 policy fraction for patterns of 999.00 rounding up  
    {  
        BOOST_TEST(test("1.09e-06",  
			double_,  
            1.09e-6  
            ));  
    }  
```  
Failes the test, with output  
```  
in test: result is false  
in test: generated "1.9e-06"  
karma/real3.cpp(202): test 'test("1.09e-06", double_, 1.09e-6 )' failed in function 'int main()'  
1 error detected.  
  
EXIT STATUS: 1  
```  
  
## Possible solutions  
  
### Minimal  
The minimal fix (in terms bytes), is to use floor(n) in the digit expression to get rid of trailing digits that promotes to computing one to much digits.  
  
[real_policies.hpp](https://github.com/boostorg/spirit/blob/b44bcac5259877633b1a6c5f662898562a66f9c2/include/boost/spirit/home/karma/numeric/real_policies.hpp#L261)  
```  
            typename remove_const<T>::type digits =   
	      (traits::test_zero(n) ? 1 : ceil(log10(floor(n) + T(1.))));  
```  
The idea is to remove the fraction part of the fraction passed (so 9.0000.....5 becomes 9.0 and the log10..ceil provides 1 in stead of 2, and thus one 0 is injected before the 9 digit.  
  
### A bit more verbose solution  
  
Other approach is based on example provided by other issues around the same function, where the computation of digits needed to format an int is factored out.  
  
A mod of that example, in-lined, and written so that it preserves the real_concept used in the tests, so that the test-suite passes,  
could look like this:   
  
```  
    template <typename OutputIterator>  
    bool fraction_part(OutputIterator& sink, T n, unsigned int precision_, unsigned int precision) const  
    {  
      using namespace std;  
      unsigned int digits=1;  
      if(! boost::spirit::traits::test_zero(n)) {  
        static constexpr uint64_t limit = UINT64_MAX / 10;  
	T num = floor(n);  
        for (uint64_t x = 10u, i = 1u;; x *= 10, i++) {  
	  if (num < x) {  
	    digits=i;break;  
	  }  
	  if (x > limit) {  
	    digits= i + 1;break;  
	  }  
	}  
      }  
          
      bool r = true;  
      for (/**/; r && digits < precision_; digits = digits + 1)  
	r = boost::spirit::karma::char_inserter<>::call(sink, '0');  
      if (precision && r)  
	r = boost::spirit::karma::int_inserter<10>::call(sink, n);  
      return r;  
    }  
```  
### Performance indications  
  
Using the performance test, examples, for generating double to strings,  
  
modified to use fmt v11,  
  
boost 1.86, arch linux, gcc 14.2.1  
  
original double_ and the two variants above gives:  
  
```  
Converting 10000000 randomly generated double values to strings.  
lib fmt v11 with precompiled {} format: 0.799861 [s]  
double_ current boost 1.86                     : 0.580689 [s]  
double_<w.minimal fix>                           : 0.632687 [s]  
double_<w. policy fix 2>                           : 0.485297 [s]  
```  
## Workarounds  
  
Thanks to customization points, the above fixes can be done using existing boost library, and overriding the generator policy with the preferred solution while waiting for fixes to be part of next.

---

## Comment 1

> Username: djowel  
> Created at: 2024-09-19 04:30:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/792#issuecomment-2359954204  

Many thanks for bringing thisn up. If you do a PR of the preferred solution, that would be wonderful!

---

## Comment 2

> Username: sigbjorn  
> Created at: 2024-09-19 08:40:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/792#issuecomment-2360379372  

I will make PR for it along the directions hinted, just need some time available, also to verify it works on some more compilers as well.

---

## Comment 3

> Username: djowel  
> Created at: 2024-09-19 10:12:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/792#issuecomment-2360577808  

> I will make PR for it along the directions hinted, just need some time available, also to verify it works on some more compilers as well.  
  
Sounds good!

---

## Comment 4

> Username: sigbjorn  
> Created at: 2024-09-19 15:25:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/792#issuecomment-2361336363  

PR and proposal added, based on develop branch,  seems that the two failing jobs are not related to  the PR
