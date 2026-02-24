# #497 - Bring numeric extension to core GIL [Closed]

> Username: lpranam  
> Created at: 2020-05-18 05:55:49 UTC  
> Updated at: 2022-02-18 09:20:23 UTC  
> Closed at: 2022-02-18 08:55:59 UTC  
> Url: https://github.com/boostorg/gil/issues/497  

Earlier GIL was a simple Image manipulation library and then it made sense to keep numeric as an extension but as we are now moving forward with the inclusion of image processing algorithms and features such as histogram, the numeric extension is a complement to the image processing module and if image processing is implemented in core GIL then numeric should also be in the core IMO.

---

## Comment 1

> Username: mloskot  
> Created at: 2020-05-18 19:24:17 UTC  
> Url: https://github.com/boostorg/gil/issues/497#issuecomment-630388592  

This is a good idea. By the way, we are also planning of moving whole or parts of the toolbox into core, see #75.  
  
- Shall we move everything in one go or feature by feature as required by our current activities (GSoc)?  
- Shall we introduce proxy headers in `boost/gil/extension/numeric` for some transition period, in case there are users of the numeric?

---

## Comment 2

> Username: lpranam  
> Created at: 2020-05-20 14:07:12 UTC  
> Url: https://github.com/boostorg/gil/issues/497#issuecomment-631496028  

> * Shall we move everything in one go or feature by feature as required by our current activities (GSoc)?  
  
IMO, it is not a huge task we can move it in one go, I'll work on it.(I'll try to complete my all pending promises too before next release :wink:)  
  
> * Shall we introduce proxy headers in `boost/gil/extension/numeric` for some transition period, in case there are users of the numeric?  
  
I would suggest that we should keep proxy header for a couple of releases, with throwing some warnings of deprecation of the header.

---

## Comment 3

> Username: mloskot  
> Created at: 2020-05-20 17:12:42 UTC  
> Url: https://github.com/boostorg/gil/issues/497#issuecomment-631608263  

> IMO, it is not a huge task we can move it in one go  
  
I asked not worrying about the size of the task but about moving unnecessary things from an extension to core. Do we need all features of numeric in core?

---

## Comment 4

> Username: lpranam  
> Created at: 2020-05-20 20:59:23 UTC  
> Updated at: 2020-05-20 21:00:00 UTC  
> Url: https://github.com/boostorg/gil/issues/497#issuecomment-631723677  

I think we can deprecate:  
`affine.hpp`, `sampler.hpp`, `resample.hpp`. If you go to `sampler.hpp` or `resampler.hpp` you'll know why I said that and `affine.hpp` is only used in those two headers.  
  
Apart from that   
`channel_numeric_operations.hpp` and `pixel_numeric_operations.hpp` can be useful many times into image processing algorithms it would make things simple in future. I am not aware if it is used anywhere into the core but it should be used.  
  
and then there are `kernel.hpp` and `convolve.hpp` which is used in core so without any doubt, we should move them and it uses functions from `algorithm.hpp` so also move that. But the primary reason to move this header into the core is not because we use it in the core, but they are already some very useful image processing algorithms which we should have in core/image_processing

---

## Comment 5

> Username: mloskot  
> Created at: 2020-05-20 21:09:31 UTC  
> Url: https://github.com/boostorg/gil/issues/497#issuecomment-631728388  

>  If you go to sampler.hpp or resampler.hpp you'll know why I said that and affine.hpp is only used in those two headers.  
  
I don't mind deprecating those.  
Those fit in the examples better indeed, which structure, by the day, could be cleaned up into folders, then `example/resample/`.   
  
Thanks for clarifying my questions. No objections from me.

---

## Comment 6

> Username: lpranam  
> Created at: 2021-12-31 01:56:09 UTC  
> Url: https://github.com/boostorg/gil/issues/497#issuecomment-1003238413  

these resampling and affine examples also have tests. I am not sure what to do with them.  
  
I typically don't like removing tests, more cases to test less the bug.  
  
But if we more those headers in examples I am not sure if it is a good idea to use example headers in the tests.(and on the other hand it will add unnecessary complications in configuration)  
  
looking for suggestions on what to do with those headers...  
@mloskot @simmplecoder @sdebionne

---

## Comment 7

> Username: mloskot  
> Created at: 2022-01-03 16:46:42 UTC  
> Url: https://github.com/boostorg/gil/issues/497#issuecomment-1004218167  

> these resampling and affine examples also have tests. I am not sure what to do with them.  
  
How about keeping these example-like to-be-deprecated headers inside `boost/gil/extension/numeric` for now?  
  
In other words  
1. move the useful stuff from `boost/gil/extension/numeric` upward into `boost/gil`  core  
2. keep the rest of the stuff inside `boost/gil/extension/numeric`  
3. keep the current structure of tests and examples for the stuff from 2.  
  
This would seem like the least disruptive approach giving us time for further refactoring.
