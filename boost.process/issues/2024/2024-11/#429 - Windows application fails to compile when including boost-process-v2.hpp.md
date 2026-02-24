# #429 - Windows application fails to compile when including boost/process/v2.hpp [Open]

> Username: keince  
> Created at: 2024-11-15 17:26:52 UTC  
> Updated at: 2025-03-27 11:47:29 UTC  
> Url: https://github.com/boostorg/process/issues/429  

I'm upgrading my application from using   
  
**#include <boost/process.hpp>**   
  
to using the new boost process v2 stuff by using   
  
**#include <boost/process/v2.hpp>**  
  
When I do this, I get the following compile errors in Visual Studio 2022...  
  
**boost\process\v2\detail\utf8.hpp(28,47): error C2491: 'boost::process::v2::detail::conv_string': definition of dllimport function not allowed  
boost\asio\detail\socket_types.hpp(24,1): fatal error C1189: #error: WinSock.h has already been included**  
  
Any ideas why I'm getting these errors? I've tried different things from previous issues posted but no success so far.  
  
I'm using VS 2022 64-bit running on Windows 11. I'm also using boost::process version 1.86 built with vcpkg

---

## Comment 1

> Username: keince  
> Created at: 2024-11-15 18:07:39 UTC  
> Url: https://github.com/boostorg/process/issues/429#issuecomment-2479637110  

Just a follow-up to my previous comment...  
  
It appears that there are issues with version 1.86 and using v2 on Windows based on this thread of discussion...  
  
https://github.com/conan-io/conan-center-index/pull/25388  
  
While there appears to be a hotfix for the problem, the boost::process built in vcpkg (which is what I'm using) doesn't have it in there.   
  
Unless something can be done to mitigate this problem, I will revert back to using the soon-to-be deprecated boost::process v1...

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-11-16 05:09:30 UTC  
> Url: https://github.com/boostorg/process/issues/429#issuecomment-2480407819  

The first was a bug, that's now fixed (for the upcoming 1.87 release). The 1.87 beta is already out and I think the release will be on December 4th. I don't know when either will land in vcpkg.  
  
As a temporary workaround, you could just grab the fixed header (https://github.com/boostorg/process/blob/develop/include/boost/process/v2/detail/utf8.hpp) and include it before `process/v2.hpp` under a different name. The include guard should then take care of things for you;.  
  
I'll probably deprecate v1 in the release after that (March).  
  
The second issue seems to have some inclusion order issues caused by some odd inclusion order. Try including `boost/asio.hpp` before anything else. For a less blunt solution I'd need to know what you include beforehand (most like the windows.h or WinSock.h is included from somewhere other than asio).

---

## Comment 3

> Username: atomictoquark  
> Created at: 2025-03-27 11:47:27 UTC  
> Url: https://github.com/boostorg/process/issues/429#issuecomment-2757752888  

for second issue,add #define WIN32_LEAN_AND_MEAN befor anything.
