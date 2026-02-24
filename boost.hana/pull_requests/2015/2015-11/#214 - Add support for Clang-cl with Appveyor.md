# #214 Add support for Clang-cl with Appveyor [Merged]

> Username: ldionne  
> Created at: 2015-11-24 20:52:19 UTC  
> Updated at: 2015-11-25 19:22:15 UTC  
> Merged at: 2015-11-25 19:21:35 UTC  
> Closed at: 2015-11-25 19:21:35 UTC  
> Url: https://github.com/boostorg/hana/pull/214  

Once merged, this is going to address #207.  
  
Todo:  
- [x] Check what is the smallest working value for `-fms-compatibility-version`, as explained in https://github.com/boostorg/hana/commit/b9d943bfcb8e65a604f950818d8f8498af59c4ca#commitcomment-14602599.  
    
  [Edit: I confirm that `-fms-compatibility-version=18` causes breakage due to missing C++14 facilities, so the smallest admissible value seems to be 19.]  
- [x] Adjust warning message when `_MSV_VER < 1900` in `<boost/hana/config.hpp>`.

---
