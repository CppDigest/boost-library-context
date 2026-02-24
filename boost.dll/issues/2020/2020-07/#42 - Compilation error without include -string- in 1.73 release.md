# #42 - Compilation error without include <string> in 1.73 release [Closed]

> Username: neyron85  
> Created at: 2020-07-02 10:04:25 UTC  
> Updated at: 2020-07-23 12:29:01 UTC  
> Closed at: 2020-07-23 12:04:25 UTC  
> Url: https://github.com/boostorg/dll/issues/42  

When i adding '#define BOOST_DLL_USE_STD_FS' in simple project, then i get compilation error.  
```c++  
//#include <string> // << Uncomment this line and compilation is ok  
  
#ifndef BOOST_DLL_USE_STD_FS  
#define BOOST_DLL_USE_STD_FS  
#endif  
  
#include <boost/dll.hpp>  
  
int main()  
{  
	return 0;  
}  
```  
![image](https://user-images.githubusercontent.com/62893064/86345625-6fffad80-bc64-11ea-8cc0-c9c83b7b17bb.png)  
  
I found that adding include <string> before definition BOOST_DLL_USE_STD_FS solves this problem.  
  
Next i looked on diffs between DLL in Boost 1.72 and 1.73 releases and seen that include of <string> was in x_info_interface.hpp and that header does not exists anymore.  
  
Best regards, Dmitry Egorov.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-07-23 12:29:01 UTC  
> Url: https://github.com/boostorg/dll/issues/42#issuecomment-662978520  

Many thanks for the report!
