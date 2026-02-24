# #96 - Test_poisson not passing on QNX 7.1 [Open]

> Username: Ashish-Gautam-22  
> Created at: 2023-10-23 04:44:35 UTC  
> Updated at: 2023-10-23 15:39:50 UTC  
> Url: https://github.com/boostorg/random/issues/96  

I am trying to validate BOOST 1.83.0 for QNX 7.1. For the same I am running the required tests on the QNX 7.1 VM to make sure everything passes. In the random package all tests except **test_poisson** and **test_negative_binomial** are passing. I have added some traces to diagnose the issue and I have found out where the issue is happening. Please help me understand if it's a QNX issue or a BOOST issue and what's the fix. Firstly, I have modified the regularised_gamma_prefix function gamma,hpp file in the following way to get some traces:  
```  
//  
// Compute (z^a)(e^-z)/tgamma(a)  
// most if the error occurs in this function:  
//  
template <class T, class Policy, class Lanczos>  
T regularised_gamma_prefix(T a, T z, const Policy& pol, const Lanczos& l)  
{  
   std::cout << " In function regularised_gamma_prefix "<< std::endl << std::flush;  
   BOOST_MATH_STD_USING  
   if (z >= tools::max_value<T>())  
      return 0;  
     
   T agh = a + static_cast<T>(Lanczos::g()) - T(0.5);  
   T prefix;  
   T d = ((z - a) - static_cast<T>(Lanczos::g()) + T(0.5)) / agh;  
  
   if(a < 1)  
   {  
      //  
      // We have to treat a < 1 as a special case because our Lanczos  
      // approximations are optimised against the factorials with a > 1,  
      // and for high precision types especially (128-bit reals for example)  
      // very small values of a can give rather erroneous results for gamma  
      // unless we do this:  
      //  
      // TODO: is this still required?  Lanczos approx should be better now?  
      //  
      if((z <= tools::log_min_value<T>()) || (a < 1 / tools::max_value<T>()))  
      {  
         // Oh dear, have to use logs, should be free of cancellation errors though:  
         return exp(a * log(z) - z - lgamma_imp(a, pol, l));  
      }  
      else  
      {  
         // direct calculation, no danger of overflow as gamma(a) < 1/a  
         // for small a.  
         return pow(z, a) * exp(-z) / gamma_imp(a, pol, l);  
      }  
   }  
   else if((fabs(d*d*a) <= 100) && (a > 150))  
   {  
      // special case for large a and a ~ z.  
      prefix = a * boost::math::log1pmx(d, pol) + z * static_cast<T>(0.5 - Lanczos::g()) / agh;  
      prefix = exp(prefix);  
   }  
   else  
   {  
      //  
      // general case.  
      // direct computation is most accurate, but use various fallbacks  
      // for different parts of the problem domain:  
      //  
      T alz = a * log(z / agh);  
      T amz = a - z;  
	  // my additions  
	  if(a>36050) std::cout <<"alz value: " << alz << " alz type: " << typeid(alz).name()<< std::endl;  
	  if(a>36050) std::cout <<"amz value: " << amz << " amz type: " << typeid(amz).name()<< std::endl;  
	  if(a>36050) std::cout <<"z   value: " << z   << " z   type "  << typeid(z).name()  << std::endl;  
	  if(a>36050) std::cout <<"a   value: " << a   << " a   type "  << typeid(a).name()  << std::endl;  
	  if(a>36050) std::cout <<"min value: " << tools::log_min_value<T>() <<" min value type: "<< typeid(tools::log_min_value<T>()).name()  << std::endl;  
	  if(a>36050) std::cout <<"max value: " << tools::log_max_value<T>() <<" max value type: "<< typeid(tools::log_max_value<T>()).name()  << std::endl;  
	  //  
      if(((std::min)(alz, amz) <= tools::log_min_value<T>()) || ((std::max)(alz, amz) >= tools::log_max_value<T>()))  
      {  
         T amza = amz / a;  
         if(((std::min)(alz, amz)/2 > tools::log_min_value<T>()) && ((std::max)(alz, amz)/2 < tools::log_max_value<T>()))  
         {  
            // compute square root of the result and then square it:  
			  
            T sq = pow(z / agh, a / 2) * exp(amz / 2);  
            prefix = sq * sq;  
			if(a>36050) std::cout << "a "<< prefix << std::endl;  
         }  
         else if(((std::min)(alz, amz)/4 > tools::log_min_value<T>()) && ((std::max)(alz, amz)/4 < tools::log_max_value<T>()) && (z > a))  
         {  
            // compute the 4th root of the result then square it twice:  
			if(a>36050) std::cout << "In first else if "<< std::endl;  
			if(a>36050) std::cout << "agh value: " << agh << " agh type " << typeid(agh).name()<< std::endl;  
            T sq = pow(z / agh, a / 4) * exp(amz / 4);  
			if(a>36050) std::cout << "sq value: " << sq << " sq type " << typeid(sq).name()<< std::endl;  
            prefix = sq * sq;  
            prefix *= prefix;  
         }  
         else if((amza > tools::log_min_value<T>()) && (amza < tools::log_max_value<T>()))  
         {  
            prefix = pow(T((z * exp(amza)) / agh), a);  
			if(a>36050) std::cout << "In second else if " << std::endl;  
         }  
         else  
         {  
            prefix = exp(alz + amz);  
         }  
      }  
      else  
      {  
         prefix = pow(T(z / agh), a) * exp(amz);  
      }  
   }  
   prefix *= sqrt(agh / boost::math::constants::e<T>()) / Lanczos::lanczos_sum_expG_scaled(a);  
   return prefix;  
}  
```  
The output that I get in QNX console is:  
  
>  In function regularised_gamma_prefix  
> alz value: 29381.7 alz type: e  
> amz value: -45421.4 amz type: e  
> z   value: 81472.4 z   type e  
> a   value: 36051 a   type e  
> min value: -11355 min value type: e  
> max value: 11356 max value type: e  
> In second else if  
>  In function pdf calling gamma_p_derivative  
>  In function regularised_gamma_prefix  
> alz value: 29381.5 alz type: e  
> amz value: -45420.4 amz type: e  
> z   value: 81472.4 z   type e  
> a   value: 36052 a   type e  
> min value: -11355 min value type: e  
> max value: 11356 max value type: e  
> In second else if  
>  In function pdf calling gamma_p_derivative  
>  In function regularised_gamma_prefix  
> alz value: 29381.3 alz type: e  
> amz value: -45419.4 amz type: e  
> z   value: 81472.4 z   type e  
> a   value: 36053 a   type e  
> min value: -11355 min value type: e  
> max value: 11356 max value type: e  
> In first else if  
> agh value: 36064.7 agh type e  
> sq value: inf sq type e  
> Error catch Throw location unknown (consider using BOOST_THROW_EXCEPTION)  
> Dynamic exception type: boost::wrapexcept<std::overflow_error>  
> std::exception::what: Error in function boost::math::gamma_p_derivative<double>(double, double): numeric overflow  
  
  
When a =36053, the first if else condition is triggered which then causes roughly this to be executed "pow(2,900)" which obviously gives an error. The value of 'a' is dictated from the "test_real_distribution.ipp" file, it's supposed to keep increasing much further beyong 36053. Please help me understand what might be the cause of the same.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-10-23 15:39:50 UTC  
> Url: https://github.com/boostorg/random/issues/96#issuecomment-1775484058  

If I'm reading this correctly, we have:  
  
```  
z = 81472.4;  
a = 36052;  
agh = 36064.7;  
```  
  
and so  
  
```  
pow(z / agh, a / 4)  
```  
  
is   
  
```  
9.01032e+3189  
```  
  
Which is within the limits (1.189731495357231765e+4932) of an extended precision long double, so if you're seeing overflow here, my suspicion is that `std::pow(long double, long double)` is really just calling the double version internally on that platform?  If so that breaks all our internal logic for what will overflow when.  
  
We have a macro BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS which when defined disables all use of (and support for) long double's by the library which appears may be necessary in this case.  I know nothing about the platform BTW, so this is just a hunch.  Also be aware that some emulators (valgrind is the prime culprit here) do not behave in the same way as the actual hardware in all cases, especially when it comes to long doubles.
