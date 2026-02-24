# #739 X3: Cover position_tagged's features with test [Closed]

> Username: denzor200  
> Created at: 2022-10-04 06:15:32 UTC  
> Updated at: 2025-05-09 09:36:19 UTC  
> Closed at: 2025-05-09 09:36:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/739  



---

## Comment 1

> Username: Kojoley  
> Created_at: 2022-10-05 23:18:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/739#issuecomment-1269095354  

Is there any particular reason for this? I'm pretty sure it is tested indirectly via error handler tests,  
  
I don't have time to deal with CI currently, maybe next week. Probably CI checksout master branch for superrepo on pull requests.

---

## Comment 2

> Username: denzor200  
> Created_at: 2022-10-06 06:57:33 UTC  
> Url: https://github.com/boostorg/spirit/pull/739#issuecomment-1269441650  

> I'm pretty sure it is tested indirectly via error handler tests,  
  
I haven't found any references to `x3::position_cache` in the tests.  
  
> Is there any particular reason for this?  
  
position_cache's internals will be patched in https://github.com/boostorg/spirit/issues/698

---

## Comment 3

> Username: Kojoley  
> Created_at: 2022-10-06 19:19:15 UTC  
> Url: https://github.com/boostorg/spirit/pull/739#issuecomment-1270570915  

> > I'm pretty sure it is tested indirectly via error handler tests,  
>   
> I haven't found any references to `x3::position_cache` in the tests.  
  
https://github.com/boostorg/spirit/blob/e30073e5bebb8102c73d1ad3588bf3f68cec2b64/include/boost/spirit/home/x3/support/utility/error_reporting.hpp#L73

---

## Comment 4

> Username: denzor200  
> Created_at: 2022-10-10 10:25:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/739#issuecomment-1273102556  

I just looked error handler tests. I can't find any test case when `x3::error_handler` used with parsing into `x3::position_tagged` attribute. Is it exists?

---

## Comment 5

> Username: denzor200  
> Created_at: 2022-10-13 12:38:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/739#issuecomment-1277544990  

PR updated. Added test case for `x3::error_handler` with `x3::position_tagged`, test case for `x3::position_cache` was deleted.

---

## Comment 6

> Username: saki7  
> Created_at: 2025-05-09 09:36:11 UTC  
> Url: https://github.com/boostorg/spirit/pull/739#issuecomment-2865861451  

closing this as per #742

---
