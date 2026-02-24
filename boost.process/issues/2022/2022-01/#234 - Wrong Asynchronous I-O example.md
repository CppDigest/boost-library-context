# #234 - Wrong Asynchronous I/O example [Open]

> Username: reddwarf69  
> Created at: 2022-01-07 10:58:10 UTC  
> Updated at: 2022-01-07 10:58:10 UTC  
> Url: https://github.com/boostorg/process/issues/234  

https://www.boost.org/doc/libs/1_78_0/doc/html/boost_process/tutorial.html#boost_process.tutorial.async_io does  
  
```  
ios.run();  
int result = c.exit_code();  
```  
  
But as https://github.com/boostorg/process/blob/develop/include/boost/process/child.hpp#L90 explains "The return value is without any meaning if the child wasn't waited for or if it was terminated". So there is a `c.wait()` missing in the example.  
  
By the way, https://www.boost.org/doc/libs/1_78_0/doc/html/boost/process/child.html has a strange `// private member functions` comment which:  
- is wrong, exit_code() and wait() are public  
- `private:` would have made more sense  
  
Also, when in https://www.boost.org/doc/libs/1_78_0/doc/html/boost/process/child.html, if you click on `exit_code()` it sends you to the unrelated https://www.boost.org/doc/libs/1_78_0/doc/html/boost/pfr/greater_equal.html#idm2430-bb.
