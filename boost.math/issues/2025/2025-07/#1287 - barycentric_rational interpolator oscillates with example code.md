# #1287 - barycentric_rational interpolator oscillates with example code [Closed]

> Username: wjheitler  
> Created at: 2025-07-14 09:34:06 UTC  
> Updated at: 2025-07-14 17:10:36 UTC  
> Closed at: 2025-07-14 15:31:16 UTC  
> Url: https://github.com/boostorg/math/issues/1287  

I am new to this, so I may be doing something daft (and/or posting in the wrong place), but when I try to interpolate the further example (Walter Kohn's data) in the [documentation ](https://www.boost.org/doc/libs/1_72_0/libs/math/doc/html/math_toolkit/barycentric.html) for the barycentric_rational interpolator over 200 equally-spaced x values over the range of the input data, it oscillates when x > ~2 (which is when the x spacing in the raw data has a step increase).   
  
```  
const int N_PTS = 200;  
-----  
boost::math::interpolators::barycentric_rational<double> b(x_range.begin(), x_range.end(), y_range.begin());  // this is in documentation  
  
// my code  
	double deltaT = (x_range.back() - x_range.front()) / (N_PTS - 1);  
	for (int i = 0; i < N_PTS - 1; ++i)   // due to floating point innaccuracy this can generate t > x_range.back() if I go all the way to N_PTS  
	{  
		double t = x_range.front() + i * deltaT;  
		double y = b(t);  
		myTRACE(L"x, y\t%lf\t%lf\n", t, y);  
	}  
```  
  
<img width="673" height="512" alt="Image" src="https://github.com/user-attachments/assets/3c6d3de8-e90d-4c44-bbe1-0212e1e914bb" />  
  
Is this just an example of the caveat in the documentation ("... it can surprise you")? If so, it might be worth mentioning explicitly in the documentation, since presumably the output would be wrong if the abscissa search was in the oscillating range?  
  
This does not occur with the modified Akima [procedure ](https://live.boost.org/doc/libs/1_75_0/libs/math/doc/html/math_toolkit/makima.html), which produces a smooth curve over the whole range.   
  
<img width="673" height="512" alt="Image" src="https://github.com/user-attachments/assets/d6d35aa1-3d74-4a29-a6ef-6d20c20917f2" />

---

## Comment 1

> Username: NAThompson  
> Created at: 2025-07-14 15:29:57 UTC  
> Updated at: 2025-07-14 15:31:14 UTC  
> Url: https://github.com/boostorg/math/issues/1287#issuecomment-3070026318  

Yup-the PCHIP, the B-splines, and the Akima interpolation will all be better behaved on this problem.  
  
IMO, the only reason to use the barycentric rational interpolation is if you really do need a rational approximation to your data for some reason.  
  
Closing as this is not a bug, but feel free to continue the conversation in this issue.

---

## Comment 2

> Username: wjheitler  
> Created at: 2025-07-14 16:33:00 UTC  
> Url: https://github.com/boostorg/math/issues/1287#issuecomment-3070221788  

Thanks for responding. I'll change from using the barycentric_rational method to Makima in my code.   
My only further comment would be that whoever is in charge of the documentation for this method might consider pointing out the issue, or changing the example. The example goes into detail about how to find an X value that yields a particular Y value, but this will presumably fail if the requested Y value lies in the range that oscillates.

---

## Comment 3

> Username: NAThompson  
> Created at: 2025-07-14 17:10:36 UTC  
> Url: https://github.com/boostorg/math/issues/1287#issuecomment-3070328530  

Will do.  
  
FWIW, my favorite interpolator is pchip.
