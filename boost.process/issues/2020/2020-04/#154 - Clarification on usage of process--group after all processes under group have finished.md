# #154 - Clarification on usage of process::group after all processes under group have finished [Open]

> Username: JoeStaines  
> Created at: 2020-04-01 16:36:19 UTC  
> Updated at: 2020-04-01 16:36:19 UTC  
> Url: https://github.com/boostorg/process/issues/154  

Currently on unix systems, there is a situation where if you use a group with a child subprocess, and the subprocess finishes naturally  (without termination from the parent), then the group object still holds a handle to a group ID which now does not exist in the current session. So trying to use that same group to invoke another subprocess will not work. Here's an example:  
  
```  
pid,pgid,cmd  
  
1,1 procA  
2,2 procB  
3,2 procC  
  
boost::process::group's handle is 2  
procC and procB finish naturally  
boost::process::group's handle stays 2 (wasn't terminated so doesn't reset to -1)  
procA tries to start procB again  
  
1,1 procA  
4,1 procB // setpgid(0, 2) fails here, so stays the same as the parent  
5,1 procC  
  
boost::process::group's handle is still 2  
Calling terminate results in trying to terminate pgid 2 which fails  
```  
  
This is in contrast to where the parent process always terminates the children, which can reuse the same object - as the act of calling `terminate()` resets the handle.  
  
Would be nice if the group could determine it is not associated with any running processes any more and reset itself. Barring that, being able to detect the situation earlier through error codes would be nice also.
