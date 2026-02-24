# #55 - fma in boost/units/cmath.hpp? [Open]

> Username: NAThompson  
> Created at: 2023-05-29 21:15:58 UTC  
> Updated at: 2023-05-29 23:17:28 UTC  
> Url: https://github.com/boostorg/units/issues/55  

I've recently had a compile fail with `fma` on `boost::units` types. However, it appears this has been implemented in `boost/units/cmath.hpp`, but it has been explicitly disabled using an `#if 0`.  
  
```  
 git show e634a7c6   
commit e634a7c6bef7180ec34387fd7bb7c0236c17a990  
Author: Steven Watanabe <steven@providere-consulting.com>  
Date:   Sat Jun 7 17:15:24 2008 +0000  
  
    Remove compiler specific code from cmath.  Disable functions which cannot be implemented easily using Boost.Math  
  
    [SVN r46217]  
```  
  
Is there any reason why we can't re-enable these code paths today?  
  
Edit: I enabled those code paths, and it appears to be working. However, I had to add a couple other overloads:  
  
```  
template<class Unit, class Y>  
inline   
BOOST_CONSTEXPR  
quantity<Unit,Y>  
fma BOOST_PREVENT_MACRO_SUBSTITUTION (const Y& q1,  
                                      const quantity<Unit,Y>& q2,  
                                      const quantity<Unit,Y>& q3)  
{  
    using std::fma;  
    return quantity<Unit,Y>::from_value(fma(q1,q2.value(),q3.value()));  
}  
  
template<class Unit, class Y>  
inline   
BOOST_CONSTEXPR  
quantity<Unit,Y>  
fma BOOST_PREVENT_MACRO_SUBSTITUTION (const quantity<Unit, Y>& q1,  
                                      const Y& q2,  
                                      const quantity<Unit,Y>& q3)  
{  
    using std::fma;  
    return quantity<Unit,Y>::from_value(fma(q1.value(),q2,q3.value()));  
}  
```  
  
I imagine the case where `a` and `x` have "inverse units" and `a*x+b`  is dimensionless could also be supported.  
  
Patchfile: [units_patch.txt](https://github.com/boostorg/units/files/11594479/units_patch.txt)
