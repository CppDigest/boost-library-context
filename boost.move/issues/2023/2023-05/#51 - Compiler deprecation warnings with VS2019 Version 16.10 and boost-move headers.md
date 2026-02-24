# #51 - Compiler deprecation warnings with VS2019 Version 16.10 and boost/move headers [Closed]

> Username: pcaswell  
> Created at: 2023-05-18 10:46:37 UTC  
> Updated at: 2024-03-03 21:35:51 UTC  
> Closed at: 2024-03-03 21:35:18 UTC  
> Url: https://github.com/boostorg/move/issues/51  

Hello Boost Move,  
I've been in discussion with people at boost smart_ptr regarding an odd issue we see with VS2019.  Here's the original discussion:  
https://github.com/boostorg/smart_ptr/issues/108  
   
Here's my setup:  
- Operating System : Windows 10 Enterprise 22H2  
- Compiler : Visual Studio 2019 Version 16.10, Compiler 19.29.30148 for x64  
- Boost : 1.77.0  
  
Here's a program that demonstrates the essence of the problem:  
```  
#include <boost/shared_ptr.hpp>  
#include <boost/make_shared.hpp>  
  
[[deprecated ("Since 1.0.0")]] int DeprecatedA()  
{  
	return 0;  
}  
  
int main()  
{  
	DeprecatedA();  
	return 0;  
}  
  
```  
  
This code should output compiler warnings (C4996) when compiled and it does providing I do not compile with these options:  
`-external:W0 -external:I"C:/ThirdParty/v3.0.0/boost-1.77.0/include/boost-1_77"`.  When these options are specified then the above program compiles without warning despite the deprecated function not being part of boost. If I comment out the `#include <boost/make_shared.hpp>` line then the compiler outputs deprecation errors as expected.  
  
Further investigation showed that commenting out warning 4996 in `boost/move/detail/config_begin.h` fixes the problem. Also enclosing the move headers in `/boost/smart_ptr/make_shared_object.hpp` (as follows) also fixes the problem.  
  
```  
#pragma warning(push)  
#include <boost/move/core.hpp>  
#include <boost/move/utility_core.hpp>  
#pragma warning(pop)  
```  
  
Fundamentally I think this is a Microsoft Compiler issue (that they've said they won't fix in VS2019).  The code works as expected on Centos with GCC 11.3.0.  However it does seem that something in the boost move headers is causing the compiler to get confused.  If the issue is something to do with the move headers then can it be fixed across the board and not only for the inclusion of boost/make_shared.hpp as I've done above ?? The product I'm working on makes extensive use of boost - I just happened to notice this issue with the inclusion of that header.  
  
Any help, thoughts and/or suggestions would be appreciated.  
Paul

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-03-03 21:35:51 UTC  
> Url: https://github.com/boostorg/move/issues/51#issuecomment-1975346577  

Many thanks for the report, in current code there is no need to disable that warning, so I removed that.
