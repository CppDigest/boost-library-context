# #212 - Missing include in protected_fixedsize_stack.hpp [Closed]

> Username: Melnytskyi  
> Created at: 2022-11-25 09:34:39 UTC  
> Updated at: 2023-03-24 12:17:52 UTC  
> Closed at: 2023-03-24 12:17:52 UTC  
> Url: https://github.com/boostorg/context/issues/212  

In windows/protected_fixedsize_stack.hpp used boost assertions, but not included  
#include <boost/assert.hpp>  
  
and then if not included "boost assert" before including "protected fixed size stack", build in windows fails

---

## Comment 1

> Username: olk  
> Created at: 2023-03-24 12:17:52 UTC  
> Url: https://github.com/boostorg/context/issues/212#issuecomment-1482710590  

ty
