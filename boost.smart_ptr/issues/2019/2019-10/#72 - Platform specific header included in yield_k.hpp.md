# #72 - Platform specific header included in yield_k.hpp [Closed]

> Username: otski  
> Created at: 2019-10-24 10:31:38 UTC  
> Updated at: 2019-10-25 04:56:45 UTC  
> Closed at: 2019-10-25 04:56:45 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/72  

Header `yield_k.hpp` has included a Windows specific Boost platform header since commit https://github.com/boostorg/smart_ptr/commit/eac6411867386a6ac2d32496bd61cf2c58eeb039 which may cause incorrect detection of Windows platform if `predef.h` has not already been included. This change affects Boost releases 1.70.0 and 1.71.0.  
  
This probably does not cause issues in the general case, but I encountered this issue in a project where a dependent library header has `#define __WINDOWS__ 0` which in turn causes the Boost predef to accidentally detect the platform as Windows since the check is written as `if defined(__WINDOWS__)`.  
  
In the above case the root cause is in the dependent library, but including the platform specific header in a generic header feels like incorrect use of Boost predef nevertheless.  
  
Proposed fix is to revert the aforementioned commit.

---

## Comment 1

> Username: pdimov  
> Created at: 2019-10-24 12:05:08 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/72#issuecomment-545886277  

I don't understand the complaint. `predef.h` just includes all the Predef headers, including `boost/predef/platform/windows_runtime.h`. I see no reason for reverting the commit to fix the issue of `__WINDOWS__` being defined incorrectly.

---

## Comment 2

> Username: otski  
> Created at: 2019-10-25 04:56:45 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/72#issuecomment-546200632  

Hmm, on a second thought you are absolutely correct. While the change broke the compilation of the project in question, it was working by luck in the first place because `predef.h` includes the headers in alphabetical order, Windows being at the bottom.  
  
As for my latter claim, including only specific predef headers is indeed a valid use case.
