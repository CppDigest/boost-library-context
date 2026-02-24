# #649 - Should we add color tables? [Closed]

> Username: NAThompson  
> Created at: 2021-06-25 20:23:18 UTC  
> Updated at: 2022-02-09 10:19:38 UTC  
> Closed at: 2022-02-09 10:19:38 UTC  
> Url: https://github.com/boostorg/math/issues/649  

After adding the bilinear interpolator I realized a good QC would be to make an png image using it, via a scientific color table.  
  
I've already written a color table code [here](https://github.com/NAThompson/DifferentiableRaytracing/blob/master/include/drt/color_maps.hpp); would it be a good match for this library?

---

## Comment 1

> Username: mborland  
> Created at: 2021-06-26 16:40:04 UTC  
> Url: https://github.com/boostorg/math/issues/649#issuecomment-869027730  

I would vote that it would be useful to have in tools; there is already other plotting functionality in there. The end user would then have a documented tool internal to the library instead of having to roll their own solution from something like matplot++.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-06-26 17:25:58 UTC  
> Url: https://github.com/boostorg/math/issues/649#issuecomment-869033284  

+1.  
  
What are you using to write the output file - Boost.GIL, or something else?

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-06-26 23:24:19 UTC  
> Url: https://github.com/boostorg/math/issues/649#issuecomment-869073277  

I've used lodepng to write the files; see [here](https://github.com/NAThompson/DifferentiableRaytracing/blob/42b999e62276eb7b42cbbf14977c46fdf5b3e675/include/drt/png.hpp). I think Boost.GIL would also be a good choice for example code.
