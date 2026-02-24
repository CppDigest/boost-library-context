# #308 - Method to compute nth roots of unity [Closed]

> Username: anshu-khare-design  
> Created at: 2020-01-20 15:34:24 UTC  
> Updated at: 2020-01-20 16:31:00 UTC  
> Closed at: 2020-01-20 16:31:00 UTC  
> Url: https://github.com/boostorg/math/issues/308  

Nth roots of unity is very significantly used in geometry.  
They are used to generate vertices of a regular polygon.   
They are also used in number theory, the theory of group characters, and the discrete Fourier transform.  
  
This is a proposal to generate a method to compute nth roots of unity wheather real or imaginary.

---

## Comment 1

> Username: NAThompson  
> Created at: 2020-01-20 15:57:38 UTC  
> Updated at: 2020-01-20 16:16:49 UTC  
> Url: https://github.com/boostorg/math/issues/308#issuecomment-576334606  

That's just  
  
```  
std::complex<double> arg{0, 2*M_PI*n/N};  
std::exp(arg);  
```  
  
If you're looking to do something interesting, try extending the Akima spline to 2D:  
  
https://dl.acm.org/doi/10.1145/360767.360779  
  
https://dl.acm.org/doi/abs/10.1145/232826.232854  
  
Here's another good one that preserves monotonicity:  
  
https://dl.acm.org/doi/10.1016/0167-8396%2885%2990014-7

---

## Comment 2

> Username: anshu-khare-design  
> Created at: 2020-01-20 16:25:42 UTC  
> Updated at: 2020-01-20 16:29:04 UTC  
> Url: https://github.com/boostorg/math/issues/308#issuecomment-576346046  

I am interested in generating an output as follows:  
  
If a user types roots_of_unity(4).  
  
It should give an output as  
(1,0), (0,1), (-1,0), (0,-1) as they are in the complex plane.  
Later using image library we can generate the polygon with the marked points in the complex plane.

---

## Comment 3

> Username: NAThompson  
> Created at: 2020-01-20 16:29:18 UTC  
> Url: https://github.com/boostorg/math/issues/308#issuecomment-576347604  

Generating textual output is really not what this library does, nor does it really provide primitives as trivial as the roots of unity.
