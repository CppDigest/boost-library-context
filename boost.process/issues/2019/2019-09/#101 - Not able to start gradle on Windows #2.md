# #101 - Not able to start gradle on Windows #2 [Closed]

> Username: saarbastler  
> Created at: 2019-09-10 11:24:08 UTC  
> Updated at: 2019-09-13 11:28:34 UTC  
> Closed at: 2019-09-13 11:28:34 UTC  
> Url: https://github.com/boostorg/process/issues/101  

I'm not sure if somebody will read my comment on the closed issue #99, so I create a new one.  
  
The samplecode of #99 is not working on windows but it is working on Ubuntu Linux.

---

## Comment 1

> Username: saarbastler  
> Created at: 2019-09-13 11:28:34 UTC  
> Url: https://github.com/boostorg/process/issues/101#issuecomment-531201560  

I tested with native Windows API (CreateProcess, CreatePipe, ...) and I got it working. I could start a gradle process and could read the stdout.  
  
After that, i played around with the example and I found a working solution on Windows:  
  
```  
  bp::async_pipe ap_in(ios);  
  ...  
  boost::process::child c("gradle.bat --stacktrace", bp::std_out > ap_out, bp::std_err > ap_out  
    , bp::std_in < ap_in  
    , ios);  
```  
  
After redirecting stdin from a empty pipe, this example is working on Windows as expected.
