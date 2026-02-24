# #149 - The use of deprecated boost/detail/endian.hpp [Closed]

> Username: Kojoley  
> Created at: 2018-09-12 10:55:24 UTC  
> Updated at: 2018-09-14 19:04:18 UTC  
> Closed at: 2018-09-14 19:04:18 UTC  
> Url: https://github.com/boostorg/math/issues/149  

```  
In file included from ../../../../boost/math/special_functions/sign.hpp:18:  
In file included from ../../../../boost/math/special_functions/detail/fp_traits.hpp:27:  
In file included from ../../../../boost/detail/endian.hpp:9:  
../../../../boost/predef/detail/endian_compat.h:11:9: warning: The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead [-W#pragma-messages]  
#pragma message("The use of BOOST_*_ENDIAN and BOOST_BYTE_ORDER is deprecated. Please include <boost/predef/other/endian.h> and use BOOST_ENDIAN_*_BYTE instead")  
        ^  
```

---

## Comment 1

> Username: NAThompson  
> Created at: 2018-09-12 15:36:29 UTC  
> Updated at: 2018-09-12 15:38:34 UTC  
> Url: https://github.com/boostorg/math/issues/149#issuecomment-420694342  

I just tried to reproduce with calls to some functions in `sign.hpp`, but couldn't get the warning. Could you give me a MWE and compiler you found this with?

---

## Comment 2

> Username: Kojoley  
> Created at: 2018-09-12 15:52:40 UTC  
> Url: https://github.com/boostorg/math/issues/149#issuecomment-420700295  

MWE is  
```cpp  
#include <boost/math/special_functions/sign.hpp>  
```  
  
You must use develop branch. https://github.com/boostorg/predef/commit/32d4581c1689370444f2e565cfbb8421d5071807

---

## Comment 3

> Username: NAThompson  
> Created at: 2018-09-12 16:22:26 UTC  
> Url: https://github.com/boostorg/math/issues/149#issuecomment-420710675  

Cool, thanks. It should be fixed in [pr150](https://github.com/boostorg/math/pull/150).
