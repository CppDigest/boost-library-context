# #13 - Add float128 support for MSVC/Clang? [Closed]

> Username: sdcb  
> Created at: 2015-11-24 02:58:08 UTC  
> Updated at: 2016-02-24 06:10:21 UTC  
> Closed at: 2016-02-24 06:10:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/13  



---

## Comment 1

> Username: jzmaddock  
> Created at: 2015-11-24 08:35:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/13#issuecomment-159195425  

How?  
  
Neither compiler supports __float128 as far as I know.  
  
The closest alternative would be to use cpp_bin_float_quad which provides the same functionality, albeit all in software and a lot slower.

---

## Comment 2

> Username: sdcb  
> Created at: 2015-11-24 12:52:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/13#issuecomment-159258419  

Yeah, cpp_bin_float_quad is what I mean. is there any performance comparison for float128 and cpp_bin_float_quad?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2015-11-24 19:25:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/13#issuecomment-159379862  

On 24/11/2015 12:52, sdflysha wrote:  
  
> Yeah, cpp_bin_float_quad is what I mean. is there any performance   
> comparison for float128 and cpp_bin_float_quad?  
  
Not really but I just ran the linpack benchmark for each and came up with:  
  
double:                                               2727 MFlops  
__float128:                                              35MFlops  
multiprecision::float128:                       35MFlops  
multiprecision::cpp_bin_float_quad:     6MFlops
