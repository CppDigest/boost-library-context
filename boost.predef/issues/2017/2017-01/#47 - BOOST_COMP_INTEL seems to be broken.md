# #47 - BOOST_COMP_INTEL seems to be broken [Closed]

> Username: theZiz  
> Created at: 2017-01-02 14:59:44 UTC  
> Updated at: 2017-01-04 08:24:53 UTC  
> Closed at: 2017-01-04 04:18:44 UTC  
> Url: https://github.com/boostorg/predef/issues/47  

I noticed, that the macro / define `BOOST_COMP_INTEL` seems to be broken. This little program compiles with `icc` 15.2, but `BOOST_COMP_INTEL` is 0:  
  
```C++  
#include <iostream>  
#include <boost/predef.h>  
  
int main(int argc,char** argv)  
{  
	std::cout << BOOST_COMP_INTEL << std::endl;  
	return 0;  
}  
```  
  
`BOOST_COMP_INTEL_DETECTION` is defined, but is 50000000, because the [macro definition](https://github.com/boostorg/predef/blob/develop/include/boost/predef/compiler/intel.h#L37) uses `BOOST_PREDEF_MAKE_10_VRP(__INTEL_COMPILER)` instead of the more reasonable `BOOST_PREDEF_MAKE_10_VVRR(__INTEL_COMPILER)`, which would return 150000000 for my used `icc` 15.2.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2017-01-04 04:18:44 UTC  
> Url: https://github.com/boostorg/predef/issues/47#issuecomment-270291711  

Fixed in 822d09f19bc2f4ea6f42da8e0be83d10ce912ce1. Thanks for finding that.

---

## Comment 2

> Username: theZiz  
> Created at: 2017-01-04 08:24:53 UTC  
> Url: https://github.com/boostorg/predef/issues/47#issuecomment-270318025  

Thanks!
