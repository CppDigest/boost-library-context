# #276 - black_scholes test won't work. [Open]

> Username: anirul  
> Created at: 2014-10-13 09:04:12 UTC  
> Updated at: 2017-03-21 04:14:45 UTC  
> Url: https://github.com/boostorg/compute/issues/276  

Was running the test set and found out that black_scholes doesn't work on my laptop.  
  
``` bash  
> ./black_scholes  
device: HD Graphics 4000  
libc++abi.dylib: terminating with uncaught exception of type boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::context_error> >: OpenCL Build Warning : Compiler build log:  
<program source>:1:7: warning: no previous prototype for function 'cnd'  
float cnd(float d) [...]  
```  
  
Seams like the compiler on OSX need predeclaration to work, with predeclaration this seems to work:  
  
``` bash  
diff --git a/example/black_scholes.cpp b/example/black_scholes.cpp  
index e78e895..952d28d 100644  
--- a/example/black_scholes.cpp  
+++ b/example/black_scholes.cpp  
@@ -69,6 +69,10 @@ int main()  
  
     // source code for black-scholes program  
     const char source[] = BOOST_COMPUTE_STRINGIZE_SOURCE(  
+         
+               // some compiler need predeclaration  
+               float cnd(float d);  
+         
         // approximation of the cumulative normal distribution function  
         float cnd(float d)  
         {  
```  
  
Maybe you should not crash on warnings?  
  
Nice work!  
##

---

## Comment 1

> Username: roshanrags  
> Created at: 2014-10-15 05:03:28 UTC  
> Updated at: 2014-10-15 05:08:42 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-59158103  

Adding `static` before the function fixes it. Probably a bug with the OS X compiler.

---

## Comment 2

> Username: roshanrags  
> Created at: 2014-10-15 05:06:02 UTC  
> Updated at: 2014-10-15 05:48:07 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-59158245  

@kylelutz While adding `static` fixes the warning, is this supposed to be the output? The error line suggests this is wrong...  
  
```  
device: GeForce GT 650M  
option 0 call price: 0.000249462  
option 0 put price: 26.2798  
error: option prices are wrong  
```  
  
Same thing with HD 4000 as well.

---

## Comment 3

> Username: anirul  
> Created at: 2014-10-15 05:33:53 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-59159810  

This is also what I get with pre-declaration.

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-10-23 03:03:01 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-60186871  

Sorry for the delay, I've been away on vacation for the past couple weeks.  
  
I've pushed a fix (PR #280) which adds `static` (thanks @roshanr95 for the idea). Could you test it out?  
  
As for the test failing, I'll have to look into this more. Could you let me know what device/platform you are using?  
  
Thanks,  
Kyle

---

## Comment 5

> Username: kylelutz  
> Created at: 2014-11-05 04:04:11 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-61757071  

@anirul PR #293 fixes an issue I saw with the black_scholes example on Intel devices. Can you try it out on your machine?

---

## Comment 6

> Username: anirul  
> Created at: 2014-11-05 08:34:16 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-61774797  

I'm having a build issue now so I cannot test it. You want me to open a new issue?  
  
```  
compute/include/boost/compute/type_traits/type_name.hpp:94:20: error: implicit instantiation of  
      undefined template 'boost::compute::detail::type_name_trait<unsigned long>'  
    return detail::type_name_trait<T>::value();  
```  
  
I'll have a look into it.

---

## Comment 7

> Username: anirul  
> Created at: 2014-11-05 08:39:35 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-61775291  

ok this is working (just the BOOST_COMPUTE_BUILD_TESTS fail, I will create a separate issue if it doesn't exist yet).  
  
```  
> ./black_scholes   
device: HD Graphics 4000  
option 0 call price: 0.000249461  
option 0 put price: 26.2798  
error: option prices are wrong  
```  
  
Is this result expected?

---

## Comment 8

> Username: anirul  
> Created at: 2014-11-06 08:22:10 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-61941176  

I get this result with the NVIDIA GPU:  
  
```  
> ./example/black_scholes   
device: GeForce GT 650M  
option 0 call price: 0.000249462  
option 0 put price: 26.2798  
error: option prices are wrong  
```  
  
and also on Intel  
  
```  
> ./example/black_scholes   
device: Intel(R) Core(TM) i7-3720QM CPU @ 2.60GHz  
option 0 call price: 0.000249461  
option 0 put price: 26.2798  
error: option prices are wrong  
```  
  
so is it default behaviour? Should I close this?

---

## Comment 9

> Username: kylelutz  
> Created at: 2014-11-06 16:16:07 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-62003843  

Yeah, those numbers aren't correct. I'm not sure what's going wrong.  
  
The expected output should be like this:  
  
```  
option 0 call price: 0.0999002  
option 0 put price: 43.0524  
```  
  
I'd keep this issue open for now.

---

## Comment 10

> Username: kylelutz  
> Created at: 2014-12-23 04:15:19 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-67920449  

Figured it out :-). These differing results are caused by the different random-number generators used between Linux and OS X. While they're both seeded with the same value, each algorithm produces different input data for the black-scholes kernel.  
  
For example I added code to print the first few input values. On Linux I get:  
  
```  
stock_price_data[0] = 23.4755  
option_strike_data[0] = 69.923  
option_years_data[0] = 2.5638  
option 0 call price: 0.0999002  
option 0 put price: 43.0524  
```  
  
and on OS X I get:  
  
```  
stock_price_data[0] = 6.04619  
option_strike_data[0] = 33.9228  
option_years_data[0] = 2.41116  
option 0 call price: 0.000249461  
option 0 put price: 26.2798  
```  
  
I've updated the test to check for the different "expected" results based on the platform. This should be pushed soon.

---

## Comment 11

> Username: kylelutz  
> Created at: 2014-12-23 06:42:57 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-67925972  

These should hopefully be fixed in PR #354. Could you test that out? Thanks.

---

## Comment 12

> Username: skozilla  
> Created at: 2014-12-23 16:12:35 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-67967313  

If different platforms or compilers use different implementations of the rand() function, wouldn't it make more sense to test the Black-Scholes function against a Monte-Carlo simulation with the same parameters?  
  
Currently we price 4,000,000 options with random stock price, strike and time to maturity triplets and then we check the price of the first option to see if it corresponds with what we would expect. This causes a discrepancy if the rand() function has a different implementation.   
  
An alternative would be to have one Black-Scholes price with fixed parameters and a corresponding Monte-Carlo simulation that would run on the GPU. Such an implementation may require more work as we may need a PRNG written in OpenCL (which may or may not be in the scope of this library)

---

## Comment 13

> Username: kylelutz  
> Created at: 2014-12-23 16:34:55 UTC  
> Url: https://github.com/boostorg/compute/issues/276#issuecomment-67970223  

Yeah,  PR #354 has more of a quick fix to get the test passing on Mac OS X and there are multiple options to improve it. We could perform the same simulation on both host and device then compare, we could have a fixed set of input data, or we could use our own random number generator.  
  
I'm leaning towards using the last one as Boost.Compute already has a number of [random-number generators and statistical distributions](http://kylelutz.github.io/compute/boost_compute/reference.html#boost_compute.reference.api_overview.random_number_generators). It should be fairly easy to use that to generate the random input values instead.
