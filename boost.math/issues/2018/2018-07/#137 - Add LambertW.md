# #137 - Add LambertW [Closed]

> Username: jzmaddock  
> Created at: 2018-07-31 17:34:56 UTC  
> Updated at: 2018-09-02 17:00:24 UTC  
> Closed at: 2018-09-02 17:00:24 UTC  
> Url: https://github.com/boostorg/math/issues/137  

Thomas Lui supplied the following code via email:  
```  
double plog(double x)  
{  
  if (x == 0) {  
    return 0;  
  }  
  
  double w0, w1;  
  if (x > 0) {  
    w0 = log(1.2 * x / log(2.4 * x / log1p(2.4 * x)));  
  } else {  
    double v = 1.4142135623730950488 * sqrt(1 + 2.7182818284590452354 * x);  
    double N2 = 10.242640687119285146 + 1.9797586132081854940 * v;  
    double N1 = 0.29289321881345247560 * (1.4142135623730950488 + N2);  
    w0 = -1 + v * (N2 + v) / (N2 + v + N1 * v);  
  }  
  
  while (true) {  
    double e = exp(w0);  
    double f = w0 * e - x;  
    w1 = w0 - f / ((e * (w0 + 1) - (w0 + 2) * f / (w0+w0 + 2)));  
    if (fabs(w0 / w1 - 1) < 1.4901161193847656e-8) {  
      break;  
    }  
    w0 = w1;  
  }  
```  
  
Update:   
  
Thomas Lui provides better code in his thesis: ​http://discovery.ucl.ac.uk/1482128/1/Luu_thesis.pdf, see routine 11, and in Github as CUDA C++ device code: ​https://github.com/thomasluu/plog/blob/master/plog.cu.  
  
We also have a more complex submission here: ​https://github.com/CzB404/lambert_w/ which includes support for complex numbered arguments.  
  
Moved from https://svn.boost.org/trac10/ticket/11027

---

## Comment 1

> Username: NAThompson  
> Created at: 2018-09-02 14:25:06 UTC  
> Url: https://github.com/boostorg/math/issues/137#issuecomment-417934471  

Quick question:  
  
Shouldn't `include/boost/math/special_functions/lambert_w_pb.hpp` be removed?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-09-02 16:57:06 UTC  
> Url: https://github.com/boostorg/math/issues/137#issuecomment-417944268  

Good catch - yes will do that now, the lookup table data can be moved to /detail/ as well.
