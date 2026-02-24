# #24 - H0F1 Failed to converge [Closed]

> Username: NAThompson  
> Created at: 2017-05-11 02:34:44 UTC  
> Updated at: 2018-04-25 02:39:30 UTC  
> Closed at: 2018-04-25 02:39:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/24  

The following code:  
  
    int main()  
    {  
        using std::cos;  
        auto z = boost::lexical_cast<boost::multiprecision::cpp_bin_float_quad>("830758237335105368403462033753302704124887023084254016541868647823752047904229528199808155648");  
        std::cout << cos(z) << std::endl;  
    }  
  
results in the following error message:  
  
```  
../multiprecision/include/boost/multiprecision/detail/functions/trig.hpp(69): fatal error: in "void boost::multiprecision::default_ops::hyp0F1(T&, const T&, const T&) [with T = boost::multiprecision::backends::cpp_bin_float<113u, (boost::multiprecision::backends::digit_base_type)2u, void, short int, -16382, 16383>]": boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<std::runtime_error> >: H0F1 Failed to Converge  
```  
  
Reproduced with `g++ 6.3` and `clang 4.0`.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2017-05-11 18:29:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/24#issuecomment-300877465  

OK, I see the issue: the value is so large that after argument reduction   
you're left with garbage.  
  
I have a trivial fix, but I guess the open question is what value should   
be returned in this situation?  My fix involves assuming that argument   
reduction in this situation yields zero, so cos(some_big_number) always   
returns 1.  std::cos appears to return garbage, and there's nothing in   
C99 to specify a convention as far as I can see?  
  
Thanks for the report, John.  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 2

> Username: NAThompson  
> Created at: 2017-05-11 21:05:58 UTC  
> Updated at: 2017-05-11 21:12:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/24#issuecomment-300916897  

So your fix would ostensibly be something like?  
  
    if (abs(theta)*std::numeric_limits<Real>::epsilon() > 1) // Domain reduction must return garbage  
    {  
        return 1; // Some sensible value that doesn't halt the computation.  
    }  
  
I think that's sensible, but it looks as if <a href="https://www.csee.umbc.edu/~phatak/645/supl/Ng-ArgReduction.pdf">Kahan</a> has given an algorithm for accurate evaluation of trig functions at huge arguments (<a href="https://people.eecs.berkeley.edu/~wkahan/testpi/nearpi.c">implementation here</a>). Maybe a todo entry?

---

## Comment 3

> Username: pabristow  
> Created at: 2017-05-12 08:45:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/24#issuecomment-301020340  

From: Nick [mailto:notifications@github.com]  
Sent: 11 May 2017 22:06  
To: boostorg/multiprecision  
Cc: Subscribed  
Subject: Re: [boostorg/multiprecision] H0F1 Failed to converge (#24)  
  
  
So your fix would ostensibly be something like?  
  
if (abs(theta)*std::numeric_limits<Real>::epsilon() > 1) // Domain reduction must return garbage  
  
{  
  
    return 1; // Some sensible value that doesn't halt the computation.  
  
}  
  
I think that's sensible, but it looks as if has given an algorithm for accurate evaluation of trig functions at huge arguments.  
I liked  
  
“Being correct is a virtue in and of itself!”  
at the end of the Kahan<https://www.csee.umbc.edu/~phatak/645/supl/Ng-ArgReduction.pdf> link ☺

---

## Comment 4

> Username: jzmaddock  
> Created at: 2017-05-13 16:07:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/24#issuecomment-301257594  

Mostly fixed in here: https://github.com/boostorg/multiprecision/commit/f2a266da6d1af62653ccd88c444dad880d31b998  
  
Correct handling of huge arguments, Kahan style, will have to wait for now.
