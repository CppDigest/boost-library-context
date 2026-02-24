# #116 - reduce_by_key [Closed]

> Username: jpola  
> Created at: 2014-05-19 10:58:11 UTC  
> Updated at: 2015-07-20 05:15:01 UTC  
> Closed at: 2015-07-20 05:14:58 UTC  
> Url: https://github.com/boostorg/compute/issues/116  

Hello,   
  
I'm looking for reduce_by_key algorithm. Is it somehow possible to obtain it using transform and reduce functions.  
  
Kuba.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-05-20 03:11:04 UTC  
> Url: https://github.com/boostorg/compute/issues/116#issuecomment-43582330  

Currently there is no `reduce_by_key()` algorithm in Boost.Compute.  
  
Calling `transform()` will apply a function to each element of an input range and write the result to an output range. The `reduce()` algorithm will apply a binary function to each element in a range and write the final reduced value to a result location. I don't think either of these can be used for this particular purpose.

---

## Comment 2

> Username: jpola  
> Created at: 2014-05-28 17:34:11 UTC  
> Url: https://github.com/boostorg/compute/issues/116#issuecomment-44439564  

That's true what you've written. It would be great to have it in compute project.

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-07-19 13:29:26 UTC  
> Url: https://github.com/boostorg/compute/issues/116#issuecomment-122661032  

It can be closed, see https://github.com/boostorg/compute/pull/477.
