# #204 - ibeta error for fisher f distribution [Closed]

> Username: blackmarlin72  
> Created at: 2019-05-10 10:59:16 UTC  
> Updated at: 2019-06-23 14:04:57 UTC  
> Closed at: 2019-06-23 14:04:57 UTC  
> Url: https://github.com/boostorg/math/issues/204  

The following call sequence  
  
`  
#include <boost/math/distributions/fisher_f.hpp>  
  
int main() {  
    double p = 1e-186; // about the break point where I start getting the error  
    boost::math::fisher_f_distribution<double> fd(11.0, 14.0);  
    double F = boost::math::quantile(boost::math::complement(fd, p));  
    return 0;  
}  
`  
  
Will throw  
  
Error: Error in function boost::math:ibeta<long double>(long double, long double, long double): Root finding evaluation exceeded 200 iterations, giving up now.  
  
I'm using boost 1.67.0.  
  
Note that a similar issue (but with a higher break point) has been reportedly fixed a long time ago here:  
https://svn.boost.org/trac10/ticket/8314

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-05-10 17:23:42 UTC  
> Url: https://github.com/boostorg/math/issues/204#issuecomment-491366494  

Confirmed.  
  
Unfortunately, with these inverse functions it's all about finding better heuristics which fix these pathological cases without breaking something else.  I have some ideas about this one, let me experiment a bit and I'll report back.
