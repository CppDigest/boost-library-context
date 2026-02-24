# #241 - boost::process:child/args not behaving the same on Ubuntu 20.04LTS as Android (embedded Linux) [Open]

> Username: Shielsy  
> Created at: 2022-03-04 11:16:50 UTC  
> Updated at: 2022-03-04 11:48:14 UTC  
> Url: https://github.com/boostorg/process/issues/241  

Boost version: 1.76  
NDK: android-ndk-r23  
ANDROID_ABI=arm64-v8a   
ANDROID_PLATFORM=android-24  
  
I'm seeing different behaviour on Ubuntu than Android using the same version of boost. Here are some examples:  
  
```cpp  
namespace bp = boost::process;  
  
bp::child c("ls");                                          // OK on Ubuntu, OK on Android  
bp::child c(bp::search_path("ls"));                         // OK on Ubuntu, fails on Android  
bp::child c(bp::search_path("ls"), bp::args("-l"));         // OK on Ubuntu, fails on Android  
```  
  
All calls were followed by `c.wait();`  
  
Error message is the same:  
`  
terminating with uncaught exception of type boost::process::process_error: execve failed: No such file or directory. Aborted  
`  
  
My PATH (Android)  
  
`  
msm8996:/data/local/tmp # echo $PATH                                                                                                                                                                                                                                           
/sbin:/vendor/bin:/system/sbin:/system/bin:/system/xbin  
`  
  
  
Has anyone else experienced this? I'm unable to pass args (second parameter) on Android. Also, boost:process::search_path doesn't seem to work at all on Android.
