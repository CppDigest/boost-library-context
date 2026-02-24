# #199 - Error in tutorial (double wait) and/or possible bug ("wait error: No child processes") [Open]

> Username: daravi  
> Created at: 2021-02-18 08:41:35 UTC  
> Updated at: 2021-02-24 03:12:57 UTC  
> Url: https://github.com/boostorg/process/issues/199  

I'm not sure if this is the right project for documentation issues. But here:  
https://www.boost.org/doc/libs/1_75_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.group  
  
It gives the following example  
```  
bp::group g;  
bp::child c("make", g);  
if (!g.wait_for(std::chrono::seconds(10))  
    g.terminate();  
  
c.wait(); //to avoid a zombie process & get the exit code  
```  
  
However this results in a runtime error in case of a successful wait:  
```  
terminating with uncaught exception of type boost::process::process_error: wait error: No child processes  
```  
I think the correct code snippet should be:  
```  
bp::group g;  
bp::child c("make", g);  
if (!g.wait_for(std::chrono::seconds(10))  
{  
    g.terminate();  
    c.wait(); //to avoid a zombie process & get the exit code  
}  
```  
  
Edit: I found a weird case. If I call `c.running()` before `c.wait()` in the crashing snippet then I do not get the exception. I think somehow the `running` call affects the internal state which the name suggests it shouldn't (even though the signature is not `const`).

---

## Comment 1

> Username: daravi  
> Created at: 2021-02-24 03:12:57 UTC  
> Url: https://github.com/boostorg/process/issues/199#issuecomment-784718398  

@klemens-morgenstern
