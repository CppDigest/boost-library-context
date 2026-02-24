# #255 - std::system() and boost::process::system() are incompatible [Open]

> Username: ghost  
> Created at: 2022-05-25 06:13:07 UTC  
> Updated at: 2022-06-04 08:50:28 UTC  
> Url: https://github.com/boostorg/process/issues/255  

The documentation states:  
  
> With the standard library this looks like this.  
```  
int result = std::system("g++ main.cpp");  
```  
>Which we can write exactly like this in boost.process.  
```  
namespace bp = boost::process; //we will assume this for all further examples  
int result = bp::system("g++ main.cpp");  
```  
  
While this documentation is technically correct, it creates a false impression that `std::system` and `boost::process::system` are interchangeable, while in fact they are not. Proof MWE:  
  
```  
#include <boost/process.hpp>  
int main()  
{  
  boost::process::system( "ls *" );  
  std::system( "ls *" );  
}  
  
```  
running `a.out` in directory with only `a.out` present produces:  
```  
lockywolf@laptop:/tmp/test/empty>./a.out   
ls: cannot access '*': No such file or directory  
a.out  
```  
  
This is either a bug in `boost::process::system` (if it is expected to match `std::system`) or it would be very nice to clarify this in the documentation.

---

## Comment 1

> Username: oliness  
> Created at: 2022-06-04 08:50:28 UTC  
> Url: https://github.com/boostorg/process/issues/255#issuecomment-1146570686  

This is because boost::process::system is, despite its name, using the POSIX `execve` function whereas std::system is using the POSIX `system` function. `execve` separates arguments and quotes them, so the behavior is different.  
  
In your example you get the same result if you do `ls "*"` in bash. That's what the boost version results in, whereas the std version is just passing the literal string so it results in a literal `ls *`  
  
As changing this would break existing programs, I propose adding to the comments and documentation that the function is actually calling `execve`, and that behavior follows `execve`. Am happy to send a PR if this is consensus?
