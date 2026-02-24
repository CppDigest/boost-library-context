# #641 - Feature Request: Allow empty feature definition value [Closed]

> Username: madmongo1  
> Created at: 2020-08-05 07:07:17 UTC  
> Updated at: 2020-09-12 21:06:36 UTC  
> Closed at: 2020-09-12 21:06:36 UTC  
> Url: https://github.com/boostorg/build/issues/641  

# Background  
  
When including boost in a cmake project, I will use cmake script to download and build boost.  
  
This involves downloading boost source, patching and then configuring.  
  
I use script to translate cmake variables to the various settings for the call to b2 in order to ensure that boost is built with the correct flags corresponding to cmake's variables.  
  
# For example  
  
```  
    list(APPEND b2_args "cxxstd=${cxxstd}")  
    if (CMAKE_CXX_FLAGS)  
        # remove any -std=xxx options as b2 has its own way of specifying c++ standard      
        string(REGEX REPLACE "-std=[^ \t\r\n$]*" "" flags "${CMAKE_CXX_FLAGS}")  
        list(APPEND b2_args "cxxflags=${flags}")  
    endif()  
    if (CMAKE_C_FLAGS)  
        list(APPEND b2_args "cflags=${CMAKE_C_FLAGS}")  
    endif()  
    if (CMAKE_EXE_LINKER_FLAGS)  
        list(APPEND b2_args "linkflags=${CMAKE_EXE_LINKER_FLAGS}")  
    endif()  
```   
  
I recently encountered a situation where the only flag in CMAKE_CXX_FLAGS was "-std=c++20", which meant that in the above snippet, `flags` resolved to an empty string. This caused the execute_process of b2 to fail.  
  
# Rationale  
  
Now of course I can simply add a further check to the values of `flags` in the above code. However this means that I have to special-case code wherever I am adding feature flags to b2 in script, i.e. the burden is on the caller.  
  
As a general rule, would it be possible to perform this empty value check within b2, the idea being that if the value is empty as per the case of `cxxflags=` then b2 would treat this as-if the feature had not been specified?  
  
This would transfer the burden to the callee, easing scripting of calls to b2.  
  
# Attachments  
  
[RequireBoost.cmake.txt](https://github.com/boostorg/build/files/5026684/RequireBoost.cmake.txt)  
Copy of RequireBoost.cmake attached for reference.  
  
Thanks for your attention.  
  
R

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-09-12 21:06:36 UTC  
> Url: https://github.com/boostorg/build/issues/641#issuecomment-691548484  

Unfortunately this is not possible. An empty feature value is already allowed with a specific different meaning.
