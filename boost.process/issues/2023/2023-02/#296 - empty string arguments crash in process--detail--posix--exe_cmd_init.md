# #296 - empty string arguments crash in process::detail::posix::exe_cmd_init [Closed]

> Username: blitzkriegoutlaw  
> Created at: 2023-02-13 19:12:44 UTC  
> Updated at: 2023-02-17 04:04:44 UTC  
> Closed at: 2023-02-17 04:04:44 UTC  
> Url: https://github.com/boostorg/process/issues/296  

This crashes on Linux:  
  
```  
#include <boost/process/child.hpp>  
  
int main()  
{  
  boost::process::child("/bin/echo \"\"");  
  return 0;  
}  
```
