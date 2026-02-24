# #18 - sort_by_key - new algorithm request [Closed]

> Username: georgevanvenrooij  
> Created at: 2013-08-12 15:11:20 UTC  
> Updated at: 2014-03-14 06:16:51 UTC  
> Closed at: 2014-03-14 06:16:51 UTC  
> Url: https://github.com/boostorg/compute/issues/18  

I have a bunch of code based on Thrust which I would like to convert over to Boost.Compute. Since my code uses thrust::sort_by_key a lot (as well as some other xxx_key functions) I was wondering if it would be possible to add this functionality to Boost.Compute as well?

---

## Comment 1

> Username: kylelutz  
> Created at: 2013-08-12 23:51:47 UTC  
> Url: https://github.com/boostorg/compute/issues/18#issuecomment-22534471  

Sounds great!  The `sort_by_key()` algorithm is definitely on my big TODO list (I just need to find some spare time). Can you let me know what key/value data types you would be using?

---

## Comment 2

> Username: georgevanvenrooij  
> Created at: 2013-08-13 09:38:50 UTC  
> Url: https://github.com/boostorg/compute/issues/18#issuecomment-22553432  

My code implements object properties as multiple arrays of a single property (to allow efficient access on GPU's). The most common value data type would be an integer (32-bit) that would index the various property arrays.  
  
Key types can be a variety of things. Some examples of cases that I have are:  
  
Simple cases:  
  
float - the simplest case  
floatN - ND data, sort by a single coordinate only (selectable at run-time)  
  
More complex:  
  
float2 - 2D data, sort by X then Y  
floatN - ND data, sort by multiple coordinates in a specified order (i.e. first X, then Z then Y)  
  
Of course integer and double key types would be very nice to have as well. :-)

---

## Comment 3

> Username: kylelutz  
> Created at: 2013-09-07 21:32:08 UTC  
> Updated at: 2013-09-07 21:32:36 UTC  
> Url: https://github.com/boostorg/compute/issues/18#issuecomment-24010169  

Hi George,  
  
I've added initial support for the `sort_by_key()` algorithm (commit is here 3389a5c741a0ce64b1e279a04770d4bcff71a891). As of right now it's only implemented for the serial insertion sort back-end so it is quite slow. But for now you can try compiling it and sorting small sequences.  
  
Thanks,  
Kyle

---

## Comment 4

> Username: kylelutz  
> Created at: 2014-03-14 06:16:51 UTC  
> Url: https://github.com/boostorg/compute/issues/18#issuecomment-37619720  

I've updated the `sort_by_key()` algorithm to use radix sort instead of insertion sort (see commit bae7432c04615d34679f92026656a0dade4083a1). Performance should now be much better.
