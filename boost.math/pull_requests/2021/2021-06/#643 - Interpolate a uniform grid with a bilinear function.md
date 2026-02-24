# #643 Interpolate a uniform grid with a bilinear function. [Merged]

> Username: NAThompson  
> Created at: 2021-06-18 22:25:26 UTC  
> Updated at: 2021-06-22 14:38:05 UTC  
> Merged at: 2021-06-22 14:38:00 UTC  
> Closed at: 2021-06-22 14:38:00 UTC  
> Url: https://github.com/boostorg/math/pull/643  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-06-19 22:36:03 UTC  
> Url: https://github.com/boostorg/math/pull/643#issuecomment-864471926  

@mborland , @jzmaddock : I got this error from the CircleCI 'inspect' tool:  
  
```  
|include|  
  include/boost/math/interpolators/bilinear_uniform.hpp:  
    C-style assert macro on line 49  
    C-style assert macro on line 50  
    Non-ASCII:         out << "(x₀, y₀) = (" << bu.x0_ << ", " << bu.y0_ << "), (Δx, Δy) = (" << bu.dx_ << ", " << bu.dy_ << "), "  
```  
  
Are non-ASCII characters in print functions actually a problem for us? I find them kinda nice . . .

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-06-20 08:26:53 UTC  
> Url: https://github.com/boostorg/math/pull/643#issuecomment-864518424  

>Are non-ASCII characters in print functions actually a problem for us? I find them kinda nice . . .  
  
That's one for the mailing list.... but if I remember correctly, folks building on windows with far-eastern character sets run into nasty issues with the source being unparsable if there's Unicode in there.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-06-22 14:37:47 UTC  
> Url: https://github.com/boostorg/math/pull/643#issuecomment-866040851  

Build error was a timeout.

---
