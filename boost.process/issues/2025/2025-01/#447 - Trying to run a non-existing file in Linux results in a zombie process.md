# #447 - Trying to run a non-existing file in Linux results in a zombie process [Open]

> Username: cmorve-te  
> Created at: 2025-01-09 12:12:46 UTC  
> Updated at: 2025-06-03 22:11:18 UTC  
> Url: https://github.com/boostorg/process/issues/447  

As can be seen in https://godbolt.org/z/4T7TMMTov, calling the process constructor on Linux (have not checked different launchers) asking it to execute a non-existing file results on a exception being thrown and the forked process never being reaped.  
  
For comparison, https://godbolt.org/z/W94K31sGW uses an existing file and it works fine.  
  
Those use Boost 1.86 since that's the latest available in Godbolt today, but I have reproduced the same issue on 1.87.

---

## Comment 1

> Username: cmorve-te  
> Created at: 2025-01-09 15:52:46 UTC  
> Url: https://github.com/boostorg/process/issues/447#issuecomment-2580627713  

A `::waitpid(pid, nullptr, 0);` before/after https://github.com/boostorg/process/blob/b529769eb5d08d9f95b0130fd2f1a49acad330f4/include/boost/process/v2/posix/default_launcher.hpp#L442 fixes the problem for me. But I will let you create a proper fix.

---

## Comment 2

> Username: mathisloge  
> Created at: 2025-05-22 19:31:34 UTC  
> Url: https://github.com/boostorg/process/issues/447#issuecomment-2902349603  

@klemens-morgenstern after switching to boost 1.88 I'm experiencing now a endless loop when creating a process for a non existing executable.   
It loops forever in ` do { ::waitpid(pid, nullptr, 0); } while (errno == EINTR);`  in https://github.com/boostorg/process/blame/15555b966440e86a8b7f64be97fd3a41e409fdd0/include/boost/process/v2/posix/default_launcher.hpp#L414C65-L414C65

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-05-23 01:11:05 UTC  
> Url: https://github.com/boostorg/process/issues/447#issuecomment-2902988839  

What OS are you running and what could be interrupting the waitpid?

---

## Comment 4

> Username: mathisloge  
> Created at: 2025-05-23 18:47:18 UTC  
> Url: https://github.com/boostorg/process/issues/447#issuecomment-2905472445  

I'm on fedora 42.   
I've now changed the precondition (either valid executable with path or the passed path has to be found in the env) before constructing the process object.   
  
> what could be interrupting the waitpid  
  
IIUC we have to check the return code of waitpid to be ret == -1 && errno == EINTR. B.c. if waitpid returns with another value then -1, inspecting errno is invalid and lead to wrong values.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2025-05-24 01:34:00 UTC  
> Url: https://github.com/boostorg/process/issues/447#issuecomment-2906263124  

Ok, so we're on the same OS.  
  
I think you are correct, errno probably does not get set to zero if there's no failure.

---

## Comment 6

> Username: GeorgeHuber  
> Created at: 2025-06-03 21:56:41 UTC  
> Updated at: 2025-06-03 22:11:17 UTC  
> Url: https://github.com/boostorg/process/issues/447#issuecomment-2937331561  

Hi,   
  
I have also ran into this same issue when creating a boost::process::child using a non-existent executable on linux. I will be unable to upgrade to a new version of boost and am hoping to check whether an executable exists on path before the call to fork to avoid this behavior altogether.   
  
I was going through the boost source code around [do { ::waitpid(pid, nullptr, 0); } while (errno == EINTR);](https://github.com/boostorg/process/blob/15555b966440e86a8b7f64be97fd3a41e409fdd0/include/boost/process/v2/posix/default_launcher.hpp#L414). It seems at first glance like a race condition where any failed execve call could lead to this behavior. Is there a reason only non-existent executables can hit the zombie/infinite loop case?  
  
Thank you for any insight!
