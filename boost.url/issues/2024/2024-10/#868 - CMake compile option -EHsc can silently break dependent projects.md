# #868 - CMake compile option /EHsc can silently break dependent projects [Closed]

> Username: sazindev  
> Created at: 2024-10-11 16:08:32 UTC  
> Updated at: 2024-10-14 18:26:31 UTC  
> Closed at: 2024-10-14 18:26:31 UTC  
> Url: https://github.com/boostorg/url/issues/868  

After upgrading Boost Url, our Windows executable started to crash due to access violations and stack corruption.  
  
The reason is that PUBLIC compile options are intrusive, and this line  
```cmake  
 target_compile_options(${target} PUBLIC $<$<CXX_COMPILER_ID:MSVC>:/EHsc>)  
```  
  
added in commit fcb5f58b0ebe64646841bffe3c52304c46a2fa71 overrides our own compile options (we are using `/EHs` for specific reasons) when calling `target_link_libraries(myproj PUBLIC Boost::url)`. The compile options from linked dependencies are added last to the compiler command line, and hence our carefully chosen `/EHs` option was silently ignored.  
  
We could devise some work-arounds, but ultimately I think this should be configurable via CMake, such that I can choose what exception handling strategy is used, and hence propagated. If Boost Url has constraints on the supported option, it would be nice to fail the CMake configure step.  
  
I do not know if a shared or static library using `/EHsc`, which is then linked to an application using `/EHs` is supported by Microsoft. If yes, `PRIVATE` instead of `PUBLIC` could be an option here. But I do know that if some module is using `/EHa`, all linked modules should use `/EHa`. So being able to configure the Boost Url exception handling mode from the outside seems like the best thing to do.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-10-11 16:13:44 UTC  
> Url: https://github.com/boostorg/url/issues/868#issuecomment-2407729250  

That (the library adding `/EHsc`) is completely wrong, libraries shouldn't mess with the exception flags. Enabling or disabling exceptions is a user choice.
