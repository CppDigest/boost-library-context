# #131 Use <boost::math::policies::real> instead of <real> [Merged]

> Username: kuhlenough  
> Created at: 2018-06-25 03:10:43 UTC  
> Updated at: 2018-06-26 08:52:40 UTC  
> Merged at: 2018-06-26 08:52:40 UTC  
> Closed at: 2018-06-26 08:52:40 UTC  
> Url: https://github.com/boostorg/math/pull/131  

When compiling for VxWorks 7   
Dinkum STL declares an inline real() function in the global namespace that clang can not distinguish from policies::real otherwise.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2018-06-25 11:37:02 UTC  
> Url: https://github.com/boostorg/math/pull/131#issuecomment-399921702  

The code changes look good to me, and the build failure appears to be a problem on Travis's side.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2018-06-25 11:52:19 UTC  
> Url: https://github.com/boostorg/math/pull/131#issuecomment-399925089  

Ah... question: do we want to have to fully qualify everything in the _examples_  remember that code code gets extracted and injected into the docs.  My gut says probably not - std::real should be hidden away inside the std namespace and not cause a conflict here?

---

## Comment 3

> Username: kuhlenough  
> Created_at: 2018-06-25 13:53:01 UTC  
> Url: https://github.com/boostorg/math/pull/131#issuecomment-399959457  

With Dinkum there's a real() in the C global namespace that's causing the problem, not std::real. I realize not many platforms are using Dinkum anymore, so the fix is specific.  If keeping the examples clean is paramount, an alternative is:    
  
```  
#if BOOST_DINKUMWARE_STDLIB >= 650  
#define <real> <boost::math::policies::real>  
#endif  
```

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-06-25 17:00:29 UTC  
> Url: https://github.com/boostorg/math/pull/131#issuecomment-400023247  

MSVC uses the Dinkum std lib and there are no issues.  So it's restricted to VxWorks.  It's also not std conforming to do that, as in:  
  
```  
std::complex<double> cd;  
// do something  
double d = real(cd); // oops  
```  
  
Unless `::real` is an alias for `std::real` ?  
  
Either way my inclination is to just let the examples fail to build on that one platform, and fix up just the tests.

---

## Comment 5

> Username: kuhlenough  
> Created_at: 2018-06-26 00:24:28 UTC  
> Updated_at: 2018-06-26 00:26:14 UTC  
> Url: https://github.com/boostorg/math/pull/131#issuecomment-400136790  

Update. MS doesn't use the <complex.h> where the conflicting  ::real() is defined, there \<complex\> is Dinkum but, not <complex.h> so this is VxWorks only issue, ( and possibly QNX, )   
We asked PJ ...  
\<pjp\>  
I believe that it is proper, and even required, to define a slew of C names in both the global and std:: namespace. The problem here is that you're asking the compiler to do too much. It has to determine which overload of real should be chosen for discrete_quantile<real>, the one you're defining or the one from the library. That's a different issue.  
  
I don't see a library bug here, but I'm always prepared to be further educated.  
  
P.J. Plauger  
\</pjp\>  
I'm not qualified to have opinion,  so I updated the pull request to keep the examples clean, but not build them for VxWorks, as @jzmaddock  suggests

---
