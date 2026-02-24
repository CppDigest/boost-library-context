# #436 - Ensure all examples build without errors [Closed]

> Username: mloskot  
> Created at: 2020-02-28 23:34:05 UTC  
> Updated at: 2021-11-10 17:21:04 UTC  
> Closed at: 2021-11-10 17:21:04 UTC  
> Url: https://github.com/boostorg/gil/issues/436  

Currently, only handful of the examples is compiled  
  
https://github.com/boostorg/gil/blob/62379dd5b97c4c2cdb50c4798412c05a6f00c8d8/example/Jamfile#L21-L27  
  
We need to ensure all programs in `example/` directory compile with `b2`:  
  
- Complete the `Jamfile` configuration  
- Verify all examples compile  
- Verify all examples build/link (i.e. replace `compile` with `exe`)  
  
https://github.com/boostorg/gil/blob/62379dd5b97c4c2cdb50c4798412c05a6f00c8d8/example/Jamfile#L33-L34

---

## Comment 1

> Username: Scramjet911  
> Created at: 2021-01-07 14:16:40 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-756143099  

So I was checking the examples and most of them are sign compare warnings,   
For the one in the hessian example (and the header file) I fixed it like this  
  
![hessian_header](https://user-images.githubusercontent.com/36035352/103896019-52316a80-5117-11eb-8f3c-5facb653460c.png)  
  
I converted it from signed to unsigned since the value of that variable is greater than 0.  
The file is running, but I wanted to make sure that this type casting won't cause other problems.

---

## Comment 2

> Username: mloskot  
> Created at: 2021-01-07 18:13:27 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-756287588  

The correct type is `std::ptrdiff_t`.  
  
Typically, the `coord_t` aliases are based on the `point_t`   
  
https://github.com/boostorg/gil/blob/e1c69d1718b902d3ecb00d83ec4683494919b751/include/boost/gil/point.hpp#L115-L117  
  
or determined from image view < locator < iterator < `difference_type`  
  
https://github.com/boostorg/gil/blob/e1c69d1718b902d3ecb00d83ec4683494919b751/include/boost/gil/step_iterator.hpp#L121-L123  
  
which in both (all) cases boils down to `std::ptrdif_t`.

---

## Comment 3

> Username: Scramjet911  
> Created at: 2021-01-08 14:57:11 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-756798332  

> The correct type is `std::ptrdiff_t`  
  
Did you mean the type of `w_y` and `w_x`? Changing that doesn't do much since as you said - `coord_t` aliases are based on the `point_t` which finally boils down to `std::ptrdif_t`  
  
Casting the `weights.size()` to `std::ptrdif_t` does fix the warnings, but wouldn't casting unsigned long to signed long cause range errors?  
  
Or we could cast `w_y` and `w_x` to `std::size_t`, but this doesn't seem right even though it works.

---

## Comment 4

> Username: mloskot  
> Created at: 2021-01-08 16:02:27 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-756836050  

AFAICT, implementation of the `compute_hessian_responses` already, implicitly, assumes   
- `dst.height() < weights.size()`  
- `dst.width()  < weights.size()`  
  
The usage semantics of `weights.size()` is as if it was either height or width within the (image) view grid of pixels .  
So, to me `static_cast<std::ptrdiff_t>(weights.size())` matches the situation outlined above, i.e. cast to domain of image/view dimensions, and not casting anything to `std::size_t`.  
  
A check for those pre-condition could be added to the function, to be on safe side, throwing an exception if not fulfilled.  
  
/cc @simmplecoder

---

## Comment 5

> Username: Scramjet911  
> Created at: 2021-01-09 04:53:03 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-757096348  

Ok, I did that, a similar warning is also present in `harris.hpp `  
https://github.com/boostorg/gil/blob/e1c69d1718b902d3ecb00d83ec4683494919b751/include/boost/gil/image_processing/harris.hpp#L59-L64  
  
Did the same there too.  
```cpp  
    for (gil::gray32f_view_t::coord_t y_kernel = 0;  
        y_kernel < static_cast<std::ptrdiff_t>(window_length);  
        ++y_kernel) {  
        for (gil::gray32f_view_t::coord_t x_kernel = 0;  
            x_kernel < static_cast<std::ptrdiff_t>(window_length);  
            ++x_kernel) {  
```  
Also there is one in `mandelbrot.cpp `   
https://github.com/boostorg/gil/blob/e1c69d1718b902d3ecb00d83ec4683494919b751/example/mandelbrot.cpp#L43-L44  
Here I thought of maybe converting the int to size_t, since it is an array index?   
```cpp  
    for (std::size_t k=0; k<num_channels<P>::value; ++k)  
        ret[k]=(typename channel_type<P>::type)(_in_color[k]*t + _out_color[k]*(1-t));  
```  
Or should I do the same as above? And can I put in a pr after fixing these warnings?

---

## Comment 6

> Username: simmplecoder  
> Created at: 2021-01-09 14:33:27 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-757249817  

The PR will be appreciated, though there are places where you can make a larger sweep of warnings. At the moment all of the examples should be built, cmake script does globbing. B2 seems to be fixed in #437

---

## Comment 7

> Username: mloskot  
> Created at: 2021-01-09 17:04:53 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-757336014  

@Scramjet911   
> Here I thought of maybe converting the int to size_t, since it is an array index?  
  
Yes, `std::size_t` is the type to use with `num_channels`, see https://github.com/boostorg/gil/pull/476

---

## Comment 8

> Username: Scramjet911  
> Created at: 2021-01-10 12:52:27 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-757471442  

> there are places where you can make a larger sweep of warnings.  
  
Yeah I am looking into the warnings coming up during B2 build, I'll try fixing whichever ones I can (starting from the examples) and ask if I have doubts.  
  
> Yes, std::size_t is the type to use with num_channels, see #476  
  
Ok.  
BTW should I ask my doubts in this issue or should I use the slack channel?

---

## Comment 9

> Username: simmplecoder  
> Created at: 2021-01-10 12:55:56 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-757471951  

@Scramjet911 if it is something that we might come back to (sometimes we want to know why we did it that way), then ask here. If it is something like "do we have feature X?" then that might be more appropriate for Slack channel.

---

## Comment 10

> Username: Scramjet911  
> Created at: 2021-01-14 19:02:49 UTC  
> Updated at: 2021-01-15 05:21:31 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-760404602  

```  
../../boost/gil/channel.hpp:651:85: warning: conversion from ‘int’ to ‘short unsigned int’ may change value [-Wconversion]  
  651 |         const BitField channel_mask = static_cast< integer_t >( parent_t::max_val ) << _first_bit;  
      |                                       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~  
```  
The result should be of type `integer_t`, right? So just adding a type cast should be enough?  
  
```cpp  
    const BitField channel_mask = static_cast< integer_t >(static_cast< integer_t >( parent_t::max_val ) << _first_bit);  
```  
  
Also there are warnings in the test files as well like this  
```  
../../boost/core/lightweight_test.hpp:181:62: warning: comparing floating point with == or != is unsafe [-Wfloat-equal]  
  181 |     bool operator()(const T& t, const U& u) const { return t == u; }  
      |                                                            ~~^~~~  
```  
should I check those too?

---

## Comment 11

> Username: simmplecoder  
> Created at: 2021-01-16 06:27:20 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-761512988  

@Scramjet911 there are places where exact floating point comparison is correct, as we expect the exact numbers. I don't think I will be able to say just by glancing over those points. In the part about bit shift, if the `integer_t` is always an unsigned type, it is guaranteed that cast is correct, otherwise I do not know, but probably correct. I'm thinking if we should use the warning suppression mechanism in some places where we know it is not an error.

---

## Comment 12

> Username: Scramjet911  
> Created at: 2021-01-16 07:12:24 UTC  
> Updated at: 2021-01-16 07:13:43 UTC  
> Url: https://github.com/boostorg/gil/issues/436#issuecomment-761520170  

> In the part about bit shift, if the `integer_t` is always an unsigned type, it is guaranteed that cast is correct, otherwise I do not know, but probably correct.   
  
`integer_t` is always an unsigned int since it is initialized here  
https://github.com/boostorg/gil/blob/0c0fe1ae79053d8aedfa95eca39329d135e4ea1c/include/boost/gil/channel.hpp#L292  
So, I guess that's fine?    
<br>  
> I'm thinking if we should use the warning suppression mechanism in some places where we know it is not an error.  
  
Yeah especially the floating point equality warnings, rest are understandable warnings but the floating point comparison seems pointless to me, since the work around is   
https://github.com/boostorg/gil/blob/0c0fe1ae79053d8aedfa95eca39329d135e4ea1c/include/boost/gil/image_processing/numeric.hpp#L47-L48  
which seems quite hacky in my opinion, though it is useful in some cases so disabling it globally would also be problematic.
