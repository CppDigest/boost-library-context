# #20 - Update CI [Closed]

> Username: vinniefalco  
> Created at: 2021-03-02 19:17:55 UTC  
> Updated at: 2022-10-31 23:09:35 UTC  
> Closed at: 2022-10-31 23:09:35 UTC  
> Url: https://github.com/boostorg/static_string/issues/20  

static_string needs the same treatment as Boost.JSON: Update the CI scripts to check msvc versions 14.0, 14.1, and 14.2 on Drone. Remove Appveyor and Travis badges and scripts if they are not already removed.

---

## Comment 1

> Username: sdarwin  
> Created at: 2021-11-23 21:47:11 UTC  
> Url: https://github.com/boostorg/static_string/issues/20#issuecomment-977196391  

A periodic update:  
  
- A pull request is outstanding to merge the earlier Drone CI files.   
- Tested Github Actions.    
- It's failing on newer clang, msvc, and cmake.    
- gcc, and earlier clang, are successful.   
- If you'd like to include GHA, the easiest method right now is to copy the config directly from the boostorg/boost-ci repository.  That has been refreshed recently by Flamefire.
