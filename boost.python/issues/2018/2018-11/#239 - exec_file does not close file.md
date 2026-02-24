# #239 - exec_file does not close file [Closed]

> Username: michaelrgibbs  
> Created at: 2018-11-02 10:43:28 UTC  
> Updated at: 2021-08-10 16:25:41 UTC  
> Closed at: 2021-08-10 16:25:41 UTC  
> Url: https://github.com/boostorg/python/issues/239  

When I run exec_file in a loop, I get an error on the 509th run. Using process monitor I can see that after each call to exec_file, CloseFile is not being called.  
  
I believe fe24ab9dd5440562e27422cd38f7de03356bfd16 fixed it in Boost Python 1.58.0 but it looks like 99022d2eb8fab7a433321b8b67249b87bacc44fb reverted it for 1.59.0 and the issue is present in all versions after.   
  
It looks as though it was changed back because of issues with Python 2.7, but the original problem with closing files still exists with Python 3.6.  
  
Tested with 1.67.0 and Python 3.6.4.  
  
Code used to reproduce:  
  
```c++  
#include <iostream>  
#include "boost/python/detail/wrap_python.hpp"  
#include "boost/python.hpp"  
  
using namespace boost::python;  
  
int main()  
{  
	int count = 0;  
	Py_Initialize();  
  
	while (count < 1000)  
	{  
		auto strScriptFile = "test.py";  
  
		object main_module = import("__main__");  
		dict globalDict = extract<dict>(main_module.attr("__dict__"));  
  
		try  
		{  
			std::cout << "Run number: " << count << std::endl;  
			exec_file(strScriptFile, globalDict);  
		}  
		catch (const error_already_set&)  
		{  
			std::cout << "Failed." << std::endl;  
			break;  
		}  
		count++;  
	}  
  
	return 0;  
}  
```

---

## Comment 1

> Username: LeslieGerman  
> Created at: 2019-12-04 11:59:57 UTC  
> Updated at: 2019-12-04 12:04:26 UTC  
> Url: https://github.com/boostorg/python/issues/239#issuecomment-561613488  

Looks like there was a fix attempt in https://github.com/boostorg/python/issues/52 and in the corresponding https://github.com/boostorg/python/pull/53 , but the fix was reverted, see https://github.com/boostorg/python/pull/53#issuecomment-189459522 .  
  
(And BTW I encountered the same bug in boost 1.68.0 with python 3.6.7)

---

## Comment 2

> Username: palotasb-conti  
> Created at: 2019-12-04 12:21:18 UTC  
> Url: https://github.com/boostorg/python/issues/239#issuecomment-561620272  

We're also having this issue with Boost Python 1.70 and Python 3.6.8.
