# #221 - How to get the command line from `boost::process::child` (or other classes)? [Open]

> Username: yaobinwen  
> Created at: 2021-09-21 20:58:16 UTC  
> Updated at: 2021-09-21 20:58:16 UTC  
> Url: https://github.com/boostorg/process/issues/221  

I'm using `boost::process:child` to launch a process as follows:  
  
```c++  
boost::process::child c(  
            boost::process::search_path("my-app"),  
            "--some-option",  
            "--verbose",  
            boost::process::std_out > ::stdout,  
            boost::process::std_in < ::stdin);  
c.wait();  
```  
  
But it looks like `boost::process::child` doesn't provide any interface to return a `string` (or a `vector`) that has the actual command line. For example, in the above case, the command line may be `/usr/local/bin/my-app --some-option --verbose`.  
  
I can surely form the `string` or `vector` by myself but I'm wondering if `boost::process` has already provided a way to get it. I searched in the documentation and examples but didn't seem to find it.  
  
(I want to get the command line string so when an error happens I can log the actual command in the log message.)
