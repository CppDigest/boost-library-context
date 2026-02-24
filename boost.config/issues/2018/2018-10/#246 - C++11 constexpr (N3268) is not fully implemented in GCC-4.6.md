# #246 - C++11 constexpr (N3268) is not fully implemented in GCC-4.6 [Closed]

> Username: morinmorin  
> Created at: 2018-10-09 23:30:09 UTC  
> Updated at: 2018-11-08 13:17:42 UTC  
> Closed at: 2018-11-08 08:48:31 UTC  
> Url: https://github.com/boostorg/config/issues/246  

@pdimov found that GCC-4.6 fails to compile this code  
```  
namespace ns {}  
  
template <typename T>  
constexpr T f(T t)  
{  
    using namespace ns; // using-directive or using-declaration  
    return t;  
}  
  
int main ()  
{  
    (void)f(0);  
}  
```  
and made a [workaround](https://github.com/boostorg/range/commit/023f45be010eb0d6841e7c6bb7167050cd57bd32) in Boost.Range by removing `BOOST_CONSTEXPR`.  
  
This means that GCC-4.6 does not implement some part (i.e. using-directives and using-declarations) of [N3268](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2011/n3268.htm), which is a last-minute fix towards C++11.  
  
Would it make sense to #define `BOOST_NO_CXX11_CONSTEXPR` on GCC-4.6?  
Boost.Unit uses this pattern extensively in `<boost/units/cmath.hpp>`.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-03 03:00:35 UTC  
> Updated at: 2018-11-03 03:02:04 UTC  
> Url: https://github.com/boostorg/config/issues/246#issuecomment-435555340  

Another case where gcc-4.6 falls down from Boost.Assign, but with a different feature  
https://github.com/boostorg/assign/pull/27/files#diff-6420de6f860d4379019eee5ad767a728R46  
  
Given the age of gcc 4.6, and that gcc 4.8 seems to have resolved these issues, not sure the potential regression of turning the feature off is worth it.

---

## Comment 2

> Username: pdimov  
> Created at: 2018-11-03 03:14:04 UTC  
> Url: https://github.com/boostorg/config/issues/246#issuecomment-435556007  

We've defined a defect macro for much less :-) so it certainly makes sense to me to define `BOOST_NO_CXX11_CONSTEXPR` for 4.6.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-11-08 08:48:31 UTC  
> Url: https://github.com/boostorg/config/issues/246#issuecomment-436917658  

Agreed, now fixed in develop.

---

## Comment 4

> Username: morinmorin  
> Created at: 2018-11-08 13:17:42 UTC  
> Url: https://github.com/boostorg/config/issues/246#issuecomment-436990532  

Thanks!
