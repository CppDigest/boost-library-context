# #225 Drone config [Merged]

> Username: sdarwin  
> Created at: 2021-01-12 17:02:14 UTC  
> Updated at: 2021-01-22 19:42:55 UTC  
> Merged at: 2021-01-22 19:42:55 UTC  
> Closed at: 2021-01-22 19:42:55 UTC  
> Url: https://github.com/boostorg/serialization/pull/225  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Comment 1

> Username: robertramey  
> Created_at: 2021-01-20 18:51:31 UTC  
> Url: https://github.com/boostorg/serialization/pull/225#issuecomment-763858139  

Looks interesting.  I'm pleased you've included this in a non-disruptive way all in one (hidden) subdirectory.  But I have a problem here.  I see test failures which don't look like failures to me.  Perhaps the compile time switches might have some issues. I dunno.  
  
2217 **passed** bin.v2\libs\serialization\test\test_class_info_save_binary_archive.test\msvc-14.1\release\cxxstd-latest-iso\threading-multi\test_class_info_save_binary_archive.test  
2218 compile-c-c++ bin.v2\libs\serialization\test\test_class_info_load_binary_archive.test\msvc-14.1\debug\address-model-32\cxxstd-latest-iso\threading-multi\test_class_info_load.obj  
2219 test_class_info_load.cpp  
2220 ...on 1100th target...  
2221 msvc.link bin.v2\libs\serialization\test\test_class_info_load_binary_archive.test\msvc-14.1\debug\address-model-32\cxxstd-17-iso\threading-multi\test_class_info_load_binary_archive.exe  
2222    Creating library bin.v2\libs\serialization\test\test_class_info_load_binary_archive.test\msvc-14.1\debug\address-model-32\cxxstd-17-iso\threading-multi\test_class_info_load_binary_archive.lib and object bin.v2\libs\serialization\test\test_class_info_load_binary_archive.test\msvc-14.1\debug\address-model-32\cxxstd-17-iso\threading-multi\test_class_info_load_binary_archive.exp  
2223 testing.capture-output bin.v2\libs\serialization\test\test_class_info_load_binary_archive.test\msvc-14.1\debug\address-model-32\cxxstd-17-iso\threading-multi\test_class_info_load_binary_archive.run  
2224 ====== BEGIN OUTPUT ======  
2225 d:\a\serialization\boost-root\libs\serialization\test\test_tools.hpp(206): invalid signature in function 'int __cdecl main(int,char *[])'  
2226 1 error detected.  
2227    
2228 EXIT STATUS: 1   
2229 ====== END OUTPUT ======  
2230

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-01-20 19:04:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/225#issuecomment-763866066  

Hi Robert,  
Where are the errors appearing? In Github Actions?  It looks like most GitHub Actions for this repo are red instead of green https://github.com/boostorg/serialization/actions  .  That's not correlated to this pull request in particular.     
  
Since the drone files are all contained within a single .drone directory, they shouldn't have any interaction with Github Actions, Travis, or other tests.

---

## Comment 3

> Username: robertramey  
> Created_at: 2021-01-20 19:42:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/225#issuecomment-763887534  

Hmmm - They're appearing right here!  I'm not sure who generates them.  I have enabled Appveyor, travis, GitHub actions and now drone.  Looks like they are from GitHub Actions to me.  
  
Here is where I found the error. https://github.com/boostorg/serialization/pull/225/checks?check_run_id=1689658194  
  
As I said - I don't know where it's come from.  But so far I've only found this error in the output generated when the drone PR is tested. Where do I look for the output for drone testing? I would be curious to see it.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2021-01-20 19:56:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/225#issuecomment-763894661  

> Where do I look for the output for drone testing?  
  
The steps to get Drone running are in the Instructions section of https://github.com/CPPAlliance/drone-ci :  
  
Visit https://drone.cpp.al  
"Authorize Drone". Click the "Authorize cppalliance-drone" button.  
Sync repositories. Click the "sync" button.  
A list of repositories will appear. For each repo, click and then choose "Activate Repository".  
In the settings page, change Configuration from .drone.yml to .drone.star. "Save".  
The drone files must be present in the target branch, so merge .drone.star into "develop" if you have not already done so.

---
