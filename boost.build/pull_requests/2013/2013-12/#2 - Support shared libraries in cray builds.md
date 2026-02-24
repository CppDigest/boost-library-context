# #2 Support shared libraries in cray builds. [Merged]

> Username: rsdale  
> Created at: 2013-12-06 19:06:12 UTC  
> Updated at: 2021-10-02 22:36:06 UTC  
> Merged at: 2013-12-12 17:53:17 UTC  
> Closed at: 2013-12-12 17:53:17 UTC  
> Url: https://github.com/boostorg/build/pull/2  

This update allows the cray.jam file to create shared libraries.  It is pretty conservative, settin g -O2 instead of O3, and suppressing inlining altogether.  The latter is because of a known bug in current cray compilers.  An update will be provided here and in compiler/cray.hpp when an updated cray compiler solves this problem.

---

## Comment 1

> Username: jhunold  
> Created_at: 2013-12-10 08:50:21 UTC  
> Url: https://github.com/boostorg/build/pull/2#issuecomment-30208410  

I would like to have some more inline comments on the whole static/dynamic/shared linker/compiler flags setup inside cray.jam. Otherwise the patch is just fine. I just remembered the "link" is used for executables, "archive" is the rule for static linking. Sorry for me being off-track.   
And an another question: Do we need to be backward-compatible?

---

## Comment 2

> Username: rsdale  
> Created_at: 2013-12-11 14:23:29 UTC  
> Url: https://github.com/boostorg/build/pull/2#issuecomment-30323711  

As regards backwards compatibility; there is nothing here that would break anything that used to work, but there are enough issues with the existing cray.jam that some cray users have been devising their own build scripts, or their own localized .jam files.   That said, we are really targeting the 8.2 and 8.3 versions of the cray C/C++ compiler that incorporate a lot of relevant bug fixes and C++11 support.  
  
On Dec 10, 2013, at 2:50 AM, Jürgen Hunold notifications@github.com wrote:  
  
> I would like to have some more inline comments on the whole static/dynamic/shared linker/compiler flags setup inside cray.jam. Otherwise the patch is just fine. I just remembered the "link" is used for executables, "archive" is the rule for static linking. Sorry for me being off-track.   
> And an another question: Do we need to be backward-compatible?  
>   
> —  
> Reply to this email directly or view it on GitHub.

---
