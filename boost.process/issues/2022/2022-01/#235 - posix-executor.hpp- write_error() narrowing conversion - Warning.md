# #235 - posix/executor.hpp: write_error() narrowing conversion - Warning [Open]

> Username: SebTV  
> Created at: 2022-01-11 08:53:24 UTC  
> Updated at: 2023-05-22 14:02:46 UTC  
> Url: https://github.com/boostorg/process/issues/235  

Line 156 of boost_1_78_0/boost/process/detail/posix/executor.hpp was modified to  use "auto" for the string length.   
`const auto len = std::strlen(msg);`  
The data[2] line however expects int.  
```  
int data[2] = {ec.value(), len + 1};  
 ::write(_pipe_sink, &data[0], sizeof(int) * 2);  
  
```  
This leads to a narrowing warning like   
`boost_1_78_0/boost/process/detail/posix/executor.hpp:156:40: error: narrowing conversion of '(((long unsigned int)len) + 1)' from 'long unsigned int' to 'int' inside { } [-Werror=narrowing]`

---

## Comment 1

> Username: arghness  
> Created at: 2022-03-23 18:45:33 UTC  
> Updated at: 2022-03-23 18:47:13 UTC  
> Url: https://github.com/boostorg/process/issues/235#issuecomment-1076696562  

I've got this same issue with gcc 11.2.1. It looks like it's still present in 1.79 beta 1. Example to recreate the warning (and error if warnings are errors, as with @SebTV  above):  
  
```cpp  
#include <boost/process/child.hpp>  
#include <boost/process/exe.hpp>  
  
int  
main(int argc, char *argv[])  
{  
  boost::process::child process(boost::process::exe = "test");  
  
  return 0;  
}  
```

---

## Comment 2

> Username: fanquake  
> Created at: 2023-05-22 14:02:46 UTC  
> Url: https://github.com/boostorg/process/issues/235#issuecomment-1557278532  

Looks like this should be closed if it was fixed with https://github.com/boostorg/process/commit/ab82e78c3db2b472356642657500f7a4b5a2857e?
