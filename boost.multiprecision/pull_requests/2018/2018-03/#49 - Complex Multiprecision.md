# #49 Complex Multiprecision [Merged]

> Username: NAThompson  
> Created at: 2018-03-15 12:10:38 UTC  
> Updated at: 2019-11-02 11:28:26 UTC  
> Merged at: 2018-04-22 16:11:18 UTC  
> Closed at: 2018-04-22 16:11:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-03-15 12:12:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373355530  

I absolutely could not figure out how to use the expression templates to implement 'polar'.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-03-15 13:04:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373368627  

>I absolutely could not figure out how to use the expression templates to implement 'polar'.  
  
It's a very tricky one, currently there is an overload:  
  
```  
template <unsigned Digits10, expression_template_option ExpressionTemplates>  
number<backends::mpc_complex_backend<Digits10> > polar(number<backends::mpfr_float_backend<Digits10>, ExpressionTemplates>const& r, number<backends::mpfr_float_backend<Digits10>, ExpressionTemplates> const& theta)  
{  
   number<backends::mpc_complex_backend<Digits10> > result(number<backends::mpfr_float_backend<Digits10>, ExpressionTemplates>(r * cos(theta)), number<backends::mpfr_float_backend<Digits10>, ExpressionTemplates>(r * sin(theta)));  
   return result;  
}  
```  
  
Which only works when both arguments are mpfr_float's.  If they're expression templates or one is  a scalar / numeric literal then you're currently out of luck.  I think I can probably fix that with enough enable_if logic but it won't be pretty!

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2018-03-15 23:51:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373558860  

>> I absolutely could not figure out how to use the expression templates to implement 'polar'.  
> It's a very tricky one, currently there is an overload:  
Sorry if I'm out of the discussion here, as I have only been involved as time allows.I guess I have a very basic question. When we get to the level of "complex",why deal with expression templates and digits at all? I naively thoughtthat complex would simply use a template parameter representing aUDT that behaves very much like a built-in type.  
Then polar would basically be a one-liner.  
  
    template<typename T> complex<T> polar(const T& __my_rho, const T& __my_theta)  
    {  
      using std::cos;  
      using std::sin;  
  
      return complex<T>(__my_rho * cos(__my_theta), __my_rho * sin(__my_theta));  
    }  
  
With kind regards, Chris  
   
    On Thursday, March 15, 2018, 2:04:45 PM GMT+1, jzmaddock <notifications@github.com> wrote:    
   
   
  
I absolutely could not figure out how to use the expression templates to implement 'polar'.  
  
  
It's a very tricky one, currently there is an overload:  
template <unsigned Digits10, expression_template_option ExpressionTemplates>  
number<backends::mpc_complex_backend<Digits10> > polar(number<backends::mpfr_float_backend<Digits10>, ExpressionTemplates>const& r, number<backends::mpfr_float_backend<Digits10>, ExpressionTemplates> const& theta)  
{  
   number<backends::mpc_complex_backend<Digits10> > result(number<backends::mpfr_float_backend<Digits10>, ExpressionTemplates>(r * cos(theta)), number<backends::mpfr_float_backend<Digits10>, ExpressionTemplates>(r * sin(theta)));  
   return result;  
}  
  
Which only works when both arguments are mpfr_float's. If they're expression templates or one is a scalar / numeric literal then you're currently out of luck. I think I can probably fix that with enough enable_if logic but it won't be pretty!  
  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-03-16 18:56:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373812441  

>Sorry if I'm out of the discussion here, as I have only been involved as time allows.I guess I have a very basic question. When we get to the level of "complex",why deal with expression templates and digits at all? I naively thoughtthat complex would simply use a template parameter representing aUDT that behaves very much like a built-in type. Then polar would basically be a one-liner.     template<typename T> complex<T> polar(const T& __my_rho, const T& __my_theta)     {       using std::cos;       using std::sin;       return complex<T>(__my_rho * cos(__my_theta), __my_rho * sin(__my_theta));     } With kind regards, Chris  
  
That's actually sort of what's in the code now, but the issue is slightly more complicated:  
  
* First off, what type is the result, given an argument T?  Since we can't specialise std::complex, and our complex number is an instance of class `number` so that we get expression templates both within the class's implementation, and when used.  Now, given a complex number type we know what the corresponding scalar type is, but given a scalar not necessarily what the corresponding complex type should be (there will be 2 of them, one using mpc, one not).  Now that I think about this, I suspect we need yet another traits class to map scalar type to complex type.  There may conceivably be some types where it could be either backend type - for example should the complex type corresponding to gmp's mpc type use mpc or not?  I guess since the user isn't using mpfr, probably not.  
* Next up, the arguments to polar may be expression templates, ie `polar(a+b, c-d)` should "just work".  
* We should also support one argument a literal type, ie `polar(1, b+c)`.  Thinking about it though, the second argument should *not* be a scalar, as the only case that doesn't represent a bug is the trivial one `polar(a, 0)`?  
  
I'm not sure how many function overloads that is, but it's a lot...

---

## Comment 5

> Username: NAThompson  
> Created_at: 2018-03-17 02:13:30 UTC  
> Updated_at: 2018-03-17 08:42:40 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373887084  

Not sure how helpful my QA is gonna be here (since you are probably aware of what does and doesn't work), but `value_type` works now, but there are quite a few overloads which need to be implemented, or documented that you shouldn't use due to expression template use:  
  
```  
./include/boost/math/quadrature/trapezoidal.hpp:65:12: error: no viable overloaded '='  
        I0 = I1;  
./include/boost/math/quadrature/trapezoidal.hpp:68:12: error: no viable overloaded '='  
        I1 = half<Real>()*I0;  
./include/boost/math/quadrature/trapezoidal.hpp:82:12: error: no viable overloaded '+='  
        I1 += sum*h;  
```  
  
Also, it looks like we need "has_mpc_builds" to the boost.config feature check (of Boost.Math), which I'll try to learn to do later today.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2018-03-17 09:50:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373907926  

>./include/boost/math/quadrature/trapezoidal.hpp:65:12: error: no viable overloaded '='  
        I0 = I1;  
./include/boost/math/quadrature/trapezoidal.hpp:68:12: error: no viable overloaded '='  
        I1 = half<Real>()*I0;  
./include/boost/math/quadrature/trapezoidal.hpp:82:12: error: no viable overloaded '+='  
        I1 += sum*h;  
  
Can you let me have reduced test cases for those?  Barring the single argument version of `polar` which I'd missed, everything else, including mixed mode arithmetic should be working now.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2018-03-17 15:03:53 UTC  
> Updated_at: 2018-03-17 15:38:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373926797  

I verified that this problem does not occur without the use of 'auto'. I believe I recall the docs for multiprecision floats warning against this practice, so I'm not sure of how relevant the unit test is.  
  
However, I managed to generate a second failure using `complex = complex*double - integer*double`, which I imagine is more relevant. Compiler: g++ 7.3.0 and clang 9.0.0. (Forgot CI Skip on this; build needs to be cancelled.  .  .)  
  
Putting these workarounds(?) into the trapezoidal quadrature, I was able to extract 100 digits of a Bessel function in the complex plane in an imperceptible amount of time. Pretty awesome . . .

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2018-03-17 19:41:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373947262  

> I verified that this problem does not occur without the use of 'auto'. I believe I recall the docs for multiprecision floats warning against this practice, so I'm not sure of how relevant the unit test is.  
  
Ah... you can't assign to an expression template - it's a compile time "recipe" for calculating the result, you can't ever change it.  
  
I'll see if maybe I can improve the error messages - perhaps by defining the assignment operator with a static_assert inside.  
  
The second case is a bug - the issue is that we have a sub-expression of scalar type which is being assigned to a complex - that works for mixed-mode arithmetic operations, but apparently I missed mixed mode function calls.... testing a fix now.

---

## Comment 9

> Username: NAThompson  
> Created_at: 2018-03-18 01:36:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373965993  

Cool, all of that works now on GCC and Clang. Just added one more failing example: C++14 complex literals. Are they to be supported? (I admit to finding them pretty dumb, but nonetheless it might be frustrating to have a compile fail due to someone putting one into a million lines of code and having to root it out.)

---

## Comment 10

> Username: NAThompson  
> Created_at: 2018-03-18 06:59:28 UTC  
> Updated_at: 2018-03-18 07:00:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373977086  

Here's a piece of code that I think will absolutely put the complex multiprecision through the wringer: It's the complex Lambert W function evaluated by tanh-sinh quadrature (see [here](http://www.orcca.on.ca/LambertW/))  
```  
  
template<class Complex>  
void test_lambert_w()  
{  
    typedef typename Complex::value_type Real;  
    using boost::math::constants::pi;  
    Complex z = {2, 3};  
    auto lw = [&z](Real v)->Complex {  
      using std::cos;  
      using std::sin;  
      using std::exp;  
      Real sinv = sin(v);  
      Real cosv = cos(v);  
  
      Real cotv = cosv/sinv;  
      Real cscv = 1/sinv;  
      Real num = (1-v*cotv)*(1-v*cotv) + v*v;  
      Complex den = z + v*cscv*exp(-v*cotv);  
      Complex t = num*(z/pi<Real>());  
      return t/den;  
    };  
  
    boost::math::quadrature::tanh_sinh<Real> integrator(15);  
    Complex Q = integrator.integrate(lw, (Real) 0 , pi<Real>());  
    std::cout << "W" << z << " = " << Q << std::endl;  
}  
```  
  
It looks like we looked (vaguely) towards this use case when returning `auto` from `integrator.integrate(f, a,b)`, but there's some `decltype/declval` stuff that's causing a mismatch.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2018-03-18 08:52:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373982216  

>Cool, all of that works now on GCC and Clang. Just added one more failing example: C++14 complex literals. Are they to be supported? (I admit to finding them pretty dumb, but nonetheless it might be frustrating to have a compile fail due to someone putting one into a million lines of code and having to root it out.)  
  
We're unable to support complex literals in the generic sense - which is to say:  
  
```  
3.0 +0.1i  
```  
  
Is always a literal of type std::complex<double> and of course the 0.1 loses precision if we then go and assign it to a wider type.  
  
Actually I suppose thinking about it, we could define a literal whose type is an expression template which only gets converted to the actual number type when you use it, but...  
  
* It would be specific to Boost.Multiprecision (though we could support std::complex too).  
* It might still suffer from loss of precision if folks use double constants, so it would only really be safe for use with integers... is that very useful?  
  
One thing I must do though is check that assignment/construction from std::complex works (I'm sure it doesn't!)

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2018-03-18 09:06:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373982888  

>It looks like we looked (vaguely) towards this use case when returning auto from integrator.integrate(f, a,b), but there's some decltype/declval stuff that's causing a mismatch.  
  
The issue is that you're instantiating tanh_sinh on a scalar type, but passing a functor that returns a complex result.  Quite rightly it complains that it can't convert the complex result back to a scalar - it doesn't know whether you really wanted the real value or it's `abs` or what.  
  
I briefly tried instantiating tanh_sinh<Complex> but couldn't get the call to integrate to compile for some inscrutable reason...

---

## Comment 13

> Username: NAThompson  
> Created_at: 2018-03-18 09:19:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373983521  

I'm having more luck with `gauss` right now--it's way less complicated. I've got it to working on `std::complex` and hopefully a little massage will get it to work with mpc.

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2018-03-18 11:21:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-373990301  

>I'm having more luck with gauss right now--it's way less complicated. I've got it to working on std::complex and hopefully a little massage will get it to work with mpc.  
  
Nod.  The internals of tanh_sinh aren't correct for this - we defined the return type to be the same as the return type of the integrand, but the actual calculations are all carried out using the template parameter Real.  It might be as simple as typedef'ing the return type and changing all the Real's inside `integrate` to the typedef name.  Or not...   
  
BTW a very simple example case would be integrating `sqrt(x)` over negative bounds - even I can get my head around that one ;)

---

## Comment 15

> Username: pabristow  
> Created_at: 2018-03-18 18:46:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-374031776  

☺    What would Johann Heinrich say about all this????  
  
From: Nick [mailto:notifications@github.com]  
Sent: 18 March 2018 07:00  
To: boostorg/multiprecision  
Cc: Subscribed  
Subject: Re: [boostorg/multiprecision] Complex Multiprecision (#49)  
  
  
Here's a piece of code that I think will absolutely put the complex multiprecision through the wringer: It's the complex Lambert W function evaluated by tanh-sinh quadrature (see here<http://www.orcca.on.ca/LambertW/>)  
  
  
  
template<class Complex>  
  
void test_lambert_w()  
  
{  
  
    typedef typename Complex::value_type Real;  
  
    using boost::math::constants::pi;  
  
    Complex z = {2, 3};  
  
    auto lw = [&z](Real v)->Complex {  
  
      using std::cos;  
  
      using std::sin;  
  
      using std::exp;  
  
      Real sinv = sin(v);  
  
      Real cosv = cos(v);  
  
  
  
      Real cotv = cosv/sinv;  
  
      Real cscv = 1/sinv;  
  
      Real num = (1-v*cotv)*(1-v*cotv) + v*v;  
  
      Complex den = z + v*cscv*exp(-v*cotv);  
  
      Complex t = num*(z/pi<Real>());  
  
      return t/den;  
  
    };  
  
  
  
    boost::math::quadrature::tanh_sinh<Real> integrator(15);  
  
    Complex Q = integrator.integrate(lw, (Real) 0 , pi<Real>());  
  
    std::cout << "W" << z << " = " << Q << std::endl;  
  
}  
  
It looks like we looked (vaguely) towards this use case when returning auto from integrator.integrate(f, a,b), but there's come decltype/declval stuff that's causing a mismatch.  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/multiprecision/pull/49#issuecomment-373977086>, or mute the thread<https://github.com/notifications/unsubscribe-auth/ABBuAffzGZxztwaVC75ctsyQ9Y9_VTxtks5tfgXSgaJpZM4SsCVQ>.

---

## Comment 16

> Username: NAThompson  
> Created_at: 2018-03-19 13:29:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-374211600  

Quick question: Is line 30 of `test/test_mpc_overloads.cpp` compiling for you? I'm getting  
  
```  
test/test_mpc_overloads.cpp:30:11:   required from 'void test_overloads() [with Complex = boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<100> >]'  
test/test_mpc_overloads.cpp:37:35:   required from here  
./include/boost/multiprecision/number.hpp:1220:8: error: no match for call to '(const boost::multiprecision::detail::number_kind_floating_pointexp_funct<boost::multiprecision::backends::mpfr_float_backend<100> >) (boost::multiprecision::backends::mpc_complex_backend<100>&, boost::multiprecision::backends::mpc_complex_backend<100>&)'  
       f(m_backend, t.backend());  
       ~^~~~~~~~~~~~~~~~~~~~~~~~  
```

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2018-03-19 16:41:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-374279891  

>Quick question: Is line 30 of test/test_mpc_overloads.cpp compiling for you?  
  
Ugh, it does with msvc, just checked gcc and it's failing :(  
  
Will investigate later.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2018-03-19 20:08:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-374354212  

>Quick question: Is line 30 of test/test_mpc_overloads.cpp compiling for you?  
  
This should be fixed now I hope.

---

## Comment 19

> Username: NAThompson  
> Created_at: 2018-03-20 01:57:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-374446243  

Just verified the fix on Apple clang 9 and g++-7.

---

## Comment 20

> Username: NAThompson  
> Created_at: 2018-03-20 06:34:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-374491026  

Not sure we're into cleaning out all the trivial stuff yet, but I'm getting a warning on g++-7.3.0 with `-Wall -Wextra`:  
  
  
```  
In file included from ../multiprecision/include/boost/multiprecision/detail/default_ops.hpp:1763:0,  
                 from ../multiprecision/include/boost/multiprecision/detail/generic_interconvert.hpp:9,  
                 from ../multiprecision/include/boost/multiprecision/number.hpp:22,  
                 from ../multiprecision/include/boost/multiprecision/cpp_int.hpp:12,  
                 from ../multiprecision/include/boost/multiprecision/cpp_bin_float.hpp:9,  
                 from test/test_trapezoidal.cpp:16:  
../multiprecision/include/boost/multiprecision/detail/functions/pow.hpp: In function 'void boost::multiprecision::default_ops::eval_pow(T&, const T&, const T&) [with T = boost::multiprecision::backends::cpp_bin_float<50>]':  
../multiprecision/include/boost/multiprecision/detail/functions/pow.hpp:534:10: warning: this statement may fall through [-Wimplicit-fallthrough=]  
          }  
          ^  
../multiprecision/include/boost/multiprecision/detail/functions/pow.hpp:536:7: note: here  
       default:  
       ^~~~~~~  
```  
  
From the code it looks like you are well aware of the implicit fallthrough and it is the inteded behavior. `#pragma GCC diagnostic ignored "-Wimplicit-fallthrough"`?

---

## Comment 21

> Username: NAThompson  
> Created_at: 2018-03-21 03:11:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-374823062  

A couple more errors pushed here:  
  
```  
test/test_mpc_overloads.cpp:53:47: fatal error: type 'double' cannot be narrowed to 'unsigned int' in initializer list [-Wc++11-narrowing]  
  auto f = [](Real x)->Complex { Complex z{x, 3.0}; return z;};  
```  
  
If instead I write `Complex z{x, 3}`, I get:  
  
```  
In file included from test/test_mpc_overloads.cpp:2:  
In file included from test/test.hpp:19:  
./include/boost/multiprecision/number.hpp:70:37: fatal error: no matching constructor for initialization of  
      'boost::multiprecision::backends::mpc_complex_backend<100>'  
      BOOST_MP_NOEXCEPT_IF(noexcept(Backend(std::declval<Backend const&>(), std::declval<unsigned>())))  
                                    ^       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./include/boost/multiprecision/detail/number_base.hpp:32:53: note: expanded from macro 'BOOST_MP_NOEXCEPT_IF'  
#  define BOOST_MP_NOEXCEPT_IF(x) BOOST_NOEXCEPT_IF(x)  
                                                    ^  
/usr/local/include/boost/config/detail/suffix.hpp:969:50: note: expanded from macro 'BOOST_NOEXCEPT_IF'  
#  define BOOST_NOEXCEPT_IF(Predicate) noexcept((Predicate))  
                                                 ^~~~~~~~~  
test/test_mpc_overloads.cpp:53:42: note: in instantiation of exception specification for 'number' requested here  
  auto f = [](Real x)->Complex { Complex z{x, 3}; return z;};  
```  
  
Got this error on clang and g++ 7.3.0.

---

## Comment 22

> Username: NAThompson  
> Created_at: 2018-03-21 08:33:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-374864497  

Here's one I hit trying to get Gaussian quadrature to support `f:Real ->{Real, Complex}`:  
  
```  
test/test_mpc_overloads.cpp:37:9: error: no match for 'operator<=' (operand types are 'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<100> >' and 'boost::multiprecision::number<boost::multiprecision::backends::mpc_complex_backend<100> >')  
   if (a <= b) {  
       ~~^~~~  
```  
  
Hit on g++ 7.3.0 and Apple clang.

---

## Comment 23

> Username: jzmaddock  
> Created_at: 2018-03-21 20:21:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-375082700  

>Here's one I hit trying to get Gaussian quadrature to support f:Real ->{Real, Complex}  
  
There are no relational operators for complex types, same with std::complex.  
  
I've pushed a partial fix for the 2-arg construction issues, but it doesn't get your particular case yet :(  
  
Re `std::result_of`: any reason not to just use `dectype`?

---

## Comment 24

> Username: NAThompson  
> Created_at: 2018-03-22 02:48:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-375161558  

> There are no relational operators for complex types, same with std::complex.  
  
This is because I am a moron and wrote `std::complex<mpc_100>` in the Gaussian quadrature test, which deduced a complex value-type. My bad.  
  
> Re std::result_of: any reason not to just use decltype?  
  
I had a difficult time with the return type while avoiding `auto`. In `tanh_sinh`, you write `auto tanh_sin(f, a, b)->decltype(...)`, but I worry that the expression templates will contort that construct into a nightmare type.

---

## Comment 25

> Username: NAThompson  
> Created_at: 2018-03-30 04:53:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-377444795  

Just tried to run the eigen tests, but b2 didn't find my eigen even though it was in `/usr/local/include`:  
  
```  
eigen-git-mirror/build$ make install  
Install the project...  
-- Install configuration: "Release"  
-- Up-to-date: /usr/local/include/eigen3/Eigen/CholmodSupport  
 sudo make install  
Password:  
Install the project...  
-- Install configuration: "Release"  
-- Up-to-date: /usr/local/include/eigen3/Eigen/CholmodSupport  
-- Up-to-date: /usr/local/include/eigen3/Eigen/StdVector  
...  
```  
  
I also ran `../../../b2 --reconfigure` to update the configuration, but I still got:  
  
```  
 ../../../b2 --reconfigure  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
    - has_gmp builds           : yes  
    - has_gmp builds           : yes  
    - has_mpfr builds          : yes  
    - has_mpfr builds          : yes  
    - has_mpfi builds          : yes  
    - has_mpfi builds          : yes  
...  
    - has_mpc builds           : yes  
    - has_mpc builds           : yes  
    - has_eigen builds         : no  
    - has_eigen builds         : no  
```  
  
This is low priority, of course, but I can't check the eigen tests . . .

---

## Comment 26

> Username: jzmaddock  
> Created_at: 2018-03-30 08:00:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-377471968  

>Just tried to run the eigen tests, but b2 didn't find my eigen even though it was in /usr/local/include  
  
If I recall correctly, that directory isn't always in the compilers include path by default, you may need:  
  
```  
../../../b2 --reconfigure include=/usr/local/include  
```

---

## Comment 27

> Username: NAThompson  
> Created_at: 2018-03-30 09:23:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-377486594  

Here's what I'm getting:  
  
```  
➜  test git:(complex) ✗ ../../../b2 --reconfigure include=/usr/local/include  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
    - has_gmp builds           : yes  
    - has_gmp builds           : yes  
    - has_mpfr builds          : yes  
    - has_mpfr builds          : yes  
    - has_mpfi builds          : yes  
    - has_mpfi builds          : yes  
    - has_eigen builds         : no  
    - has_eigen builds         : no  
```  
Also tried ` ../../../b2 --reconfigure --compiler=clang++ --toolset=clang include=/usr/local/include`, with the same results. Eigen definitely present:  
  
```  
$ ls /usr/local/include/eigen3/Eigen  
Cholesky               Geometry               MetisSupport           SPQRSupport            SparseQR               src  
CholmodSupport         Householder            OrderingMethods        SVD                    StdDeque  
Core                   IterativeLinearSolvers PaStiXSupport          Sparse                 StdList  
Dense                  Jacobi                 PardisoSupport         SparseCholesky         StdVector  
Eigen                  KLUSupport             QR                     SparseCore             SuperLUSupport  
Eigenvalues            LU                     QtAlignedMalloc        SparseLU               UmfPackSupport  
```

---

## Comment 28

> Username: jzmaddock  
> Created_at: 2018-03-30 09:35:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-377488861  

Ah, it's not in `/usr/local/include`, it's in `/usr/local/include/eigen3`

---

## Comment 29

> Username: NAThompson  
> Created_at: 2018-04-10 07:09:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/49#issuecomment-379997286  

> Ah, it's not in /usr/local/include, it's in /usr/local/include/eigen3  
  
Just got back to this issue: When you do a source build of `eigen`, it places the files in `/usr/local/include/eigen3`. But the Eigen docs don't say (for example) `#include <eigen3/Eigen/Dense>`, they just say `#include <Eigen/Dense>`. Presumably they aggravated some poeple with the move to Eigen3, and then so now you have to add an include path to the compiler options.

---
