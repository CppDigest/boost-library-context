# #251 - basic shell command without arguments doesn't work in posix [Open]

> Username: grtowel1510f  
> Created at: 2022-05-21 14:57:35 UTC  
> Updated at: 2022-05-21 14:57:35 UTC  
> Url: https://github.com/boostorg/process/issues/251  

a simple shell command like `echo 1` doesn't work in posix.  
  
test:  
```  
#include <boost/process.hpp>  
  
namespace bp = boost::process;  
  
int main() {  
    return bp::system("echo 1", bp::shell);  
}  
```  
  
this is a workaround:  
```  
#include <boost/process.hpp>  
  
namespace bp = boost::process;  
  
int main() {  
    return bp::system(bp::shell().string(), "-c", "echo 1");  
}  
```  
  
I have a fix that I will submit as a pull request.
