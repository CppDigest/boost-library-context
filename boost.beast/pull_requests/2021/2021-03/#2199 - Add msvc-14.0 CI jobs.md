# #2199 Add msvc-14.0 CI jobs [Closed]

> Username: sdarwin  
> Created at: 2021-03-17 16:11:59 UTC  
> Updated at: 2021-03-19 15:37:26 UTC  
> Closed at: 2021-03-19 15:37:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2199  

For discussion - not ready to merge.  
Of the three groups of tests (tests, run-fat-tests, example), one of them completes successfully, the others reach 99% completion and then hang.   
Attempting to debug, I've tried these steps:  
- Run the same test in gh actions. Same result.  
- Add a series of "echo", or "true" commands at the end of the test script. However, it does not get to those final commands.  
- Turn on debugging on the drone agent. No messages.  
- Directly observe the docker logs of the running jobs. However, they are the same as usual log output.  
The Dockerfiles are at https://github.com/CPPAlliance/droneconverter/tree/master/dockers  
@madmongo1, could the main beast test suite be broken into ten smaller pieces? And run as separate jobs. That would be a "git bisect" type strategy for debugging.  
Or, @grisumbras just commented: "I discovered the source of msvc-14.0 problem: it doesn't like template <void* = nullptr> You have to replace it with template <class = void>"

---

## Comment 1

> Username: sdarwin  
> Created_at: 2021-03-19 15:37:26 UTC  
> Url: https://github.com/boostorg/beast/pull/2199#issuecomment-802922053  

From the slack discussion, it sounds like Beast does not support msvc-14.0.  Therefore, closing the issue.

---
