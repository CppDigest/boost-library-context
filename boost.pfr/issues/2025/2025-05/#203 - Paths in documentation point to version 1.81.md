# #203 - Paths in documentation point to version 1.81 [Closed]

> Username: mk-huawei  
> Created at: 2025-05-21 08:41:42 UTC  
> Updated at: 2025-06-19 15:36:46 UTC  
> Closed at: 2025-06-19 15:36:46 UTC  
> Url: https://github.com/boostorg/pfr/issues/203  

Apparently all paths point to version 1.81 instead of to the current one.  
  
Example, at https://live.boost.org/doc/libs/develop/doc/html/reference_section_of_pfr.html (or https://live.boost.org/doc/libs/1_88_0/doc/html/reference_section_of_pfr.html), the header "Header <boost/pfr.hpp>" points to https://live.boost.org/doc/libs/1_81_0/boost/pfr.hpp.  
  
In some cases, the targets do not exist (names have changed or the file didn't exist in 1.81).  
This is the case with:  
* https://live.boost.org/doc/libs/1_81_0/boost/pfr/config.hpp  
* https://live.boost.org/doc/libs/1_81_0/boost/pfr/core_name.hpp  
* https://live.boost.org/doc/libs/1_81_0/boost/pfr/traits.hpp  
* https://live.boost.org/doc/libs/1_81_0/boost/pfr/traits_fwd.hpp

---

## Comment 1

> Username: mk-huawei  
> Created at: 2025-05-21 08:43:18 UTC  
> Url: https://github.com/boostorg/pfr/issues/203#issuecomment-2897118006  

I guess this section comes from autodoc_pfr.xml, but I don't know how it is generated.

---

## Comment 2

> Username: mk-huawei  
> Created at: 2025-05-21 08:45:06 UTC  
> Url: https://github.com/boostorg/pfr/issues/203#issuecomment-2897123112  

Update: reference to 1_81_0 is in https://github.com/boostorg/pfr/blob/develop/doc/Jamfile.v2. I guess it should be modified when bumping the version or handled differently (for develop).
