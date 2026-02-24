# #33 - 'endian': ambiguous symbol [Closed]

> Username: Eddie-cz  
> Created at: 2019-03-12 09:43:09 UTC  
> Updated at: 2019-03-13 19:52:47 UTC  
> Closed at: 2019-03-13 19:52:47 UTC  
> Url: https://github.com/boostorg/endian/issues/33  

Recent commit in buffers.hpp omitted to fully qualify namespace resulting in ambiguous symbol error when including boost::spirit headers too. Suggested fix: use boost::endian::* everywhere instead of endian::*. Environment: Windows 10, msvc15, boost 1.7.0 beta1 RC3.  
How to reproduce:  
#include <boost/spirit/include/qi.hpp>  
#include <boost/endian/arithmetic.hpp>  
  
struct record  
{  
	boost::endian::big_int16_t type;  
  
	record(boost::int16_t t)  
	{  
		type = t;		  
	}  
};

---

## Comment 1

> Username: pdimov  
> Created at: 2019-03-13 16:37:34 UTC  
> Url: https://github.com/boostorg/endian/issues/33#issuecomment-472503365  

Should be fixed now, although the test doesn't pass on master because of  
  
```  
C:\projects\boost-root\boost/endian/buffers.hpp(60) : warning C4005: 'BOOST_ENDIAN_DEFAULT_CONSTRUCT' : macro redefinition  
C:\projects\boost-root\boost/spirit/home/support/detail/endian/endian.hpp(55) : see previous definition of 'BOOST_ENDIAN_DEFAULT_CONSTRUCT'  
```  
  
and `warnings-as-errors=on`.
