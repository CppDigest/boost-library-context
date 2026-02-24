# #23 - Assertion failure in cpp_bin_float/transcendental.hpp [Closed]

> Username: NAThompson  
> Created at: 2017-05-03 23:48:32 UTC  
> Updated at: 2017-05-15 17:42:23 UTC  
> Closed at: 2017-05-15 17:42:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/23  

The following code fails with an assertion:  
  
```  
#include <cmath>  
#include <boost/multiprecision/cpp_bin_float.hpp>  
  
template<class Real>  
void reproduce_bug()  
{  
    using std::exp;  
    Real z = boost::lexical_cast<Real>("3.12833694842469108932394792349501441046376844238532467151174529142282458046122837753372144306478e+304");  
    std::cout << exp(-z) << std::endl;  
}  
  
int main()  
{  
    reproduce_bug<boost::multiprecision::cpp_bin_float_quad>();  
}  
```  
  
  
This resuls in the following error message:  
  
    a.out: ../multiprecision/include/boost/multiprecision/cpp_bin_float/transcendental.hpp:113: void boost::multiprecision::backends::eval_exp(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&) [with unsigned int Digits = 113u; boost::multiprecision::backends::digit_base_type DigitBase = (boost::multiprecision::backends::digit_base_type)2u; Allocator = void; Exponent = short int; Exponent MinE = -16382; Exponent MaxE = 16383]: Assertion `t.compare(default_ops::get_constant_ln2<cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> >()) < 0' failed.  
  
Using  
  
    $g++ --version  
    g++ (Ubuntu 6.3.0-12ubuntu2) 6.3.0 20170406

---

## Comment 1

> Username: ckormanyos  
> Created at: 2017-05-04 22:22:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/23#issuecomment-299325662  

> The following code fails an assertion:  
That is interesting. We need to look at this.  
What compiler? What general settings like optimization?  
  
Sometimes automatic type inference is tricky with multiprecision.Multiprecision, in its default state, uses expression templates.These are challenging for compilers doing type inference.  
I wonder what happens if the definition of f0 is made a bitmore specific like...  
  auto f0 = [](Real x) -> Real { return log(x)*exp(-x); };  
  
Best regards, Chris  
  
   
  
    On Thursday, May 4, 2017 1:48 AM, Nick <notifications@github.com> wrote:  
   
  
 The following code fails an assertion:template<class Real>  
void reproduce_bug()  
{  
    auto f0 = [](Real x) { return log(x)*exp(-x); };  
    Real z = boost::lexical_cast<Real>("3.12833694842469108932394792349501441046376844238532467151174529142282458046122837753372144306478e+304");  
    std::cout << "z = " << z << std::endl;  
    std::cout << f0(z) << std::endl;  
}  
  
int main()  
{  
    reproduce_bug<boost::multiprecision::cpp_bin_float_quad>();  
}  
This resuls in the following error message:a.out: ../multiprecision/include/boost/multiprecision/cpp_bin_float/transcendental.hpp:113: void boost::multiprecision::backends::eval_exp(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&) [with unsigned int Digits = 113u; boost::multiprecision::backends::digit_base_type DigitBase = (boost::multiprecision::backends::digit_base_type)2u; Allocator = void; Exponent = short int; Exponent MinE = -16382; Exponent MaxE = 16383]: Assertion `t.compare(default_ops::get_constant_ln2<cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> >()) < 0' failed.  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2017-05-04 22:38:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/23#issuecomment-299328702  

>> The following code fails an assertion:  
> That is interesting. We need to look at this.  
Actually, the program crashes in a BOOST_ASSERTin eval_exp() in transcendental.hpp.  
  
Maybe the exponent of z is so large that there is simplyan underflow problem with exp(-z)? It works when zhas been reduced Order(30).  
  
This seems confusing.  
  
Best regards, Chris   
  
    On Thursday, May 4, 2017 1:48 AM, Nick <notifications@github.com> wrote:  
   
  
 The following code fails an assertion:template<class Real>  
void reproduce_bug()  
{  
    auto f0 = [](Real x) { return log(x)*exp(-x); };  
    Real z = boost::lexical_cast<Real>("3.12833694842469108932394792349501441046376844238532467151174529142282458046122837753372144306478e+304");  
    std::cout << "z = " << z << std::endl;  
    std::cout << f0(z) << std::endl;  
}  
  
int main()  
{  
    reproduce_bug<boost::multiprecision::cpp_bin_float_quad>();  
}  
This resuls in the following error message:a.out: ../multiprecision/include/boost/multiprecision/cpp_bin_float/transcendental.hpp:113: void boost::multiprecision::backends::eval_exp(boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&, const boost::multiprecision::backends::cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE>&) [with unsigned int Digits = 113u; boost::multiprecision::backends::digit_base_type DigitBase = (boost::multiprecision::backends::digit_base_type)2u; Allocator = void; Exponent = short int; Exponent MinE = -16382; Exponent MaxE = 16383]: Assertion `t.compare(default_ops::get_constant_ln2<cpp_bin_float<Digits, DigitBase, Allocator, Exponent, MinE, MaxE> >()) < 0' failed.  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 3

> Username: NAThompson  
> Created at: 2017-05-04 23:44:24 UTC  
> Updated at: 2017-05-04 23:46:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/23#issuecomment-299338844  

Well, it should underflow to zero, and in fact, that's what it does if you *increase* the argument. For instance, changing `z` to  
  
    Real z = boost::lexical_cast<Real>("3.12e+384");  
  
makes the assert failure go away. I ran it under AddressSanitizer and UndefinedBehaviorSanitizer, but it came up clean.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2017-05-05 07:30:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/23#issuecomment-299400450  

On 04/05/2017 23:38, Christopher Kormanyos wrote:  
> >> The following code fails an assertion:  
> > That is interesting. We need to look at this.  
> Actually, the program crashes in a BOOST_ASSERTin eval_exp() in   
> transcendental.hpp.  
>  
> Maybe the exponent of z is so large that there is simplyan underflow   
> problem with exp(-z)? It works when zhas been reduced Order(30).  
  
If the assert is being triggered then that's a bug (ie internal program   
logic failure).   Thanks for looking at this, let me know if you need a   
hand, John.  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 5

> Username: jzmaddock  
> Created at: 2017-05-13 18:04:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/23#issuecomment-301264416  

I have a fix for this I'm testing now...

---

## Comment 6

> Username: jzmaddock  
> Created at: 2017-05-15 17:42:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/23#issuecomment-301548891  

Fixed in develop now.
