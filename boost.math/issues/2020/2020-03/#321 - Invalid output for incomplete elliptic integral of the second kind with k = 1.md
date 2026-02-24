# #321 - Invalid output for incomplete elliptic integral of the second kind with k = 1 [Closed]

> Username: shatalinra  
> Created at: 2020-03-06 13:26:58 UTC  
> Updated at: 2020-04-23 14:02:23 UTC  
> Closed at: 2020-04-23 14:02:23 UTC  
> Url: https://github.com/boostorg/math/issues/321  

Directory: boost/math/special_functions/  
Files: ellint_2.hpp  
  
Inside function ellint_e_imp in file ellint_2.hpp there is a special branch at 74 line for case when k = 1. It seems the original idea was that in this case the expression in the integral becomes sqrt(1 - sin(phi)^2). Using trigonometry, it simplifies to sqrt(cos(phi)^2) which is always non-negative. However, it is wrong to substitute that expression with just cos(phi) which can be negative. Therefore it is a mistake to just replace integral with sine function in that case and intergal suddenly loses its monotonically increasing property.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-03-06 14:55:49 UTC  
> Url: https://github.com/boostorg/math/issues/321#issuecomment-595804211  

@shatalinra : Do you have a main program which can demonstrate what you expect to happen?

---

## Comment 2

> Username: shatalinra  
> Created at: 2020-03-06 16:27:34 UTC  
> Url: https://github.com/boostorg/math/issues/321#issuecomment-595847771  

Here is a minimal reproducible example showing that at k = 1 function suddenly stop producing monotonically increasing values if angle is close to pi.  
```  
#include <boost/math/special_functions/ellint_2.hpp>  
  
int main()  
{  
	float a = boost::math::ellint_2(0.9f, 0.9f * boost::math::constants::pi<float>());  
	float b = boost::math::ellint_2(0.9f, 1.1f * boost::math::constants::pi<float>());  
	float c = boost::math::ellint_2(1.0f, 0.9f * boost::math::constants::pi<float>());  
	float d = boost::math::ellint_2(1.0f, 1.1f * boost::math::constants::pi<float>());  
  
	BOOST_ASSERT(a < b);  
	BOOST_ASSERT(c < d);  
  
	return 0;  
}  
```

---

## Comment 3

> Username: ckormanyos  
> Created at: 2020-04-01 14:51:13 UTC  
> Url: https://github.com/boostorg/math/issues/321#issuecomment-607295725  

> ... the integral becomes sqrt(1 - sin(phi)^2). Using trigonometry, it simplifies to sqrt(cos(phi)^2) which is always non-negative...  
  
I can take a look into this.  
  
Is it sufficient to simply use an absolute value?  
  
Or is it necessary to do anything more complicated for small  arguments near pi/2 to do a Taylor series or any additional algorithmic operations such as that?  
  
Kind regards, Chris

---

## Comment 4

> Username: shatalinra  
> Created at: 2020-04-01 20:41:56 UTC  
> Url: https://github.com/boostorg/math/issues/321#issuecomment-607479341  

@ckormanyos : could you elaborate what you meant by using an absolute value? If it is about integrand sqrt(cos(phi)^2), then yes it could be replaced with abs(cos(phi)). But I don't think integrating abs is the easiest way of solving this.  
  
Actually I already implemented workaround in my code but sadly don't have time to properly contribute it. I took idea of using integrand's periodicity from 84 line at ellint_2.hpp file and just took some shortcuts due to k = 1:  
```  
bool invert = false;  
if (phi < 0.0f)  
{  
	invert = true;  
	phi = -phi;  
}  
  
// for k = 1 ellipse actually turns to a line and every pi/2 in phi is exactly 1 in arc length   
float step = float(CV_PI) / 2.0f;  
size_t m = size_t(parameter / step);  
  
// remaining part of phi is between 0 and pi/2, so its cosinus is non-negative. Therefore we can drop abs part and now integral can be actually substituted with sinus  
float remains = parameter - m * step;  
float value = m + sin(remains);  
  
// negative arc length for negative phi  
return invert ? -value : value;  
```

---

## Comment 5

> Username: ckormanyos  
> Created at: 2020-04-01 20:45:39 UTC  
> Url: https://github.com/boostorg/math/issues/321#issuecomment-607480977  

> ... could you elaborate what you meant by using an absolute value?  
  
> Actually I already implemented workaround in my code...  
  
I mean something exactly like your workaround.  
  
Thank you.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2020-04-07 21:01:03 UTC  
> Url: https://github.com/boostorg/math/issues/321#issuecomment-610618135  

Bug confirmed.  Getting the periodicity right is tricky - your fix didn't *quite* cover it for me, testing a fix now....

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-04-08 09:01:17 UTC  
> Url: https://github.com/boostorg/math/issues/321#issuecomment-610841049  

I get a periodicity of PI, with:  
  
```  
E(phi, 1) = 2n + sin(phi - n * pi) ; -pi/2 <= phi - n * pi <= pi / 2  
```  
  
See PR referenced above.

---

## Comment 8

> Username: shatalinra  
> Created at: 2020-04-08 09:25:04 UTC  
> Url: https://github.com/boostorg/math/issues/321#issuecomment-610851795  

@jzmaddock thanks for pointing out my mistake. Your fix indeed gets periodicity right.
