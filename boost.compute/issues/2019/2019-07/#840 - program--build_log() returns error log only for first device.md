# #840 - program::build_log() returns error log only for first device [Open]

> Username: Perl99  
> Created at: 2019-07-03 20:09:08 UTC  
> Updated at: 2019-08-01 01:28:25 UTC  
> Url: https://github.com/boostorg/compute/issues/840  

When you create a context with more than one device (like both Intel CPU and Intel GPU) and there is build error, the build_log() method only returns log for first device. It should do so for each device in the context.  
  
In my case, build for the CPU was successful, but for the GPU had error. The build log only contained info about CPU.

---

## Comment 1

> Username: rosenrodt  
> Created at: 2019-08-01 01:28:24 UTC  
> Url: https://github.com/boostorg/compute/issues/840#issuecomment-517081820  

The build log is implemented in `programs.hpp` as:  
  
```c++  
 /// Returns the build log.  
std::string build_log() const  
{  
    return get_build_info<std::string>(CL_PROGRAM_BUILD_LOG, get_devices().front());  
}  
```  
  
You can try to change this line to something like this  
```c++  
 /// Returns the build log.  
std::string build_log() const  
{  
    std::string full_log;  
    for (auto& device : get_devices())  
    {  
        full_log = full_log +   
                   device.name() +   
                   '\n' +   
                   get_build_info<std::string>(CL_PROGRAM_BUILD_LOG, device);  
    }  
    return full_log;  
}  
```
