# #342 - next.hpp float_prior(infinity) returns max but float_next(max) does not return infinity [Open]

> Username: pabristow  
> Created at: 2020-04-17 13:59:10 UTC  
> Updated at: 2020-04-21 12:00:20 UTC  
> Url: https://github.com/boostorg/math/issues/342  

I was surprised to find an asymmetry in the behaviour of float_next and float_prior if the x value is near infinity.  
  
With float_prior, as I expected (hoped?), stepping back from infinity gives max.  
```    
  //  float_prior(infinity) to give max  
  
std::cout << "float_prior(std::numeric_limits<float>::infinity()) = "<< float_prior(std::numeric_limits<float>::infinity()) << std::endl;  
     // float_prior(std::numeric_limits<float>::infinity()) = 3.40282347e+38 == (std::numeric_limits<float>::max)()  
```  
With float_next, I expected the reverse to be possible, stepping forward from max to infinity.  
```  
  // float_next(max) to give infinity  
  
     std::cout << "float_next((std::numeric_limits<float>::max)()) = "<< float_next((std::numeric_limits<float>::max)()) << std::endl;  
```  
but the result is  
```  
     // Error in function float_next<float>(float): Overflow Error  
```  
The relevant bits of code are in  
  
  I:\boost\boost\math\special_functions\next.hpp  
  
```  template <class T, class Policy>  
  T float_next_imp(const T& val, const boost::true_type&, const Policy& pol)  
  
    if(val >= tools::max_value<T>())  
        return policies::raise_overflow_error<T>(function, 0, pol);  
```  
whereas in float_prior, infinity gets special treatment.  
  
```  
  template <class T, class Policy>  
  T float_prior_imp(const T& val, const boost::true_type&, const Policy& pol)  
  {  
     int fpclass = (boost::math::fpclassify)(val);  
  
     if((fpclass == (int)FP_NAN) || (fpclass == (int)FP_INFINITE))  
     {  
        if(val > 0)  
           return tools::max_value<T>();  
  
        return policies::raise_domain_error<T>(  
           function,  
           "Argument must be finite, but got %1%", val, pol);  
     }  
  ...  
  
This doesn't feel quite right?  Perhaps  
  
    if(val > tools::max_value<T>())  
        return policies::raise_overflow_error<T>(function, 0, pol);  
        // But unclear how value can ever be > max_value ?  
  
    if (val == tools::max_value<T>())  
    {  
      if (std::numeric_limits<float>::has_infinity)  
      {  
        return std::numeric_limits<FPT>::infinity();  
      }  
      else  
      {  
        return policies::raise_overflow_error<T>(function, 0, pol);  
      }  
    }  
  
Is this behaviour derived from the definition of C nextafter?  
  
But perhaps changing it now is too dangerous?  
  
At least, should this 'feature' be documented?  
And highlighted in Boost.tests

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-04-17 20:54:17 UTC  
> Url: https://github.com/boostorg/math/issues/342#issuecomment-615458941  

Interesting . . .  
  
BTW do the binary integer representations of nan and max differ by 1?

---

## Comment 2

> Username: pabristow  
> Created at: 2020-04-21 08:48:36 UTC  
> Url: https://github.com/boostorg/math/issues/342#issuecomment-617044653  

Well - yes, may be and no.  
  
Really I think that is a non-question ;-)  
  
The next 'up a bit' is one of the infinities.  
  
But infinity is a range of bit patterns.  
  
So you can't get to other infinities by a one bit change, so no.  
  
If you output in hex and in dec, you get the output "inf inf" - for all +ve infinities.  
It feels right to me for float_next and float_prior to segue from max to inf, and back, but the choice is arbitrary.  It doesn't feel right to only go one way, and not the other (current behaviour).  
  
It also seems reasonable to throw if one tries to float_next (infinity) - but one could argue for it just being still infinity.  float_next(infinity) sounds most probably a programming error?  
  
(And of course to throw for all NaNs)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2020-04-21 10:05:26 UTC  
> Url: https://github.com/boostorg/math/issues/342#issuecomment-617083650  

I think it's the other way around: there is only one true infinity representation, but there are multiple NaN's possible: https://en.wikipedia.org/wiki/IEEE_754-1985#Positive_and_negative_infinity  
  
>BTW do the binary integer representations of nan and max differ by 1?  
  
No, NaN's can have the fraction part set to anything it wants (except all zeros).  In the original design I believe the idea was that NaN's could have a "payload" that represents additional information - on why the NaN was generated for example.  But no one has been daft enough to use them for that ;)

---

## Comment 4

> Username: pabristow  
> Created at: 2020-04-21 11:30:12 UTC  
> Url: https://github.com/boostorg/math/issues/342#issuecomment-617121016  

Correct, I had mis-remembered.   
  
So the answer to Nick's question is yes, the +ve infinity   
(std::numeric_limits<>::infinity)()  
is one bit more than (std::numeric_limits<>::max)().  
  
at least for the IEEE754 floating-point types.  
  
But the behaviour of float_next and float_prior is entirely within the choice of Boost.Math author?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2020-04-21 12:00:20 UTC  
> Url: https://github.com/boostorg/math/issues/342#issuecomment-617136156  

>But the behaviour of float_next and float_prior is entirely within the choice of Boost.Math author?  
  
Haha, yes :)
