# #1123 - failure and crashes installing or building boost 1.90.0 in Visual Studio 2026 c++ solution even with v143 build tools installed [Open]

> Username: donr484  
> Created at: 2026-01-31 15:38:46 UTC  
> Updated at: 2026-01-31 17:11:45 UTC  
> Url: https://github.com/boostorg/boost/issues/1123  

Is there any way to use boost in Visual Studio 2026 c++ solutions using msvc and the latest everything?  
  
I tried downloading the latest 1.90.0 and building, but it failed (complaining about not having v143 build tools).  
  
I tried installing the v143 build tools and forcing those to build boost, but it failed.  
  
I tried adding boost to my vcpkg.json dependencies, updating baseline, and vcpkg install, but it failed. This locked up my pc and forced me to cold restart to get responsiveness again.  
  
I tried using git to get the latest vcpkg before install, and it failed. This crashed my pc into oblivion, forcing a cold restart to get back into windows. (I reported this to the vcpkg github repo.)  
  
Note that I'm new to c++, so any help is appreciated. I've been relying on microsoft.copilot.com to get me this far, but I've hit a wall.

---

## Comment 1

> Username: donr484  
> Created at: 2026-01-31 17:11:45 UTC  
> Url: https://github.com/boostorg/boost/issues/1123#issuecomment-3828854155  

I removed boost as a dependency in vcpkg.json, then added about 20 or so individual boost components that I wanted to try.   
  
vcpkg install was able to finish without crashing my pc, but it failed for boost-url. I made sure I wasn't using #include for boost-url.  
  
I then tried to do a rebuild of my solution and it froze my pc. I had to cold reboot to get responsiveness again.  
  
The last build messages i saw were waiting for lock on .vcpkg-root during the release build. (Similar messages happened before that on the debug build, but didn't lock up my pc.)  
  
--  
  
Next I tried removing all but the 2 boost components I was actually using in code from dependencies and #includes (lexical_cast and thread).   
  
Vcpkg install worked without errors.   
  
Building the solution finally worked without crashing.  
  
--  
  
There are major issues no matter how you try to put boost in your solution.  
  
I feel that I should be able to add boost as a dependency and vcpkg install should work without crashing my pc.  
  
I feel that I should be able to #include some of the boost components after that and building the solution shouldn't crash my pc.  
  
Let me know if there is anything more I can do. Thanks.
