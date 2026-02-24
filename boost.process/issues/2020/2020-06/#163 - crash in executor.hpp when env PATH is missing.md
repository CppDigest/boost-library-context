# #163 - crash in executor.hpp when env PATH is missing [Open]

> Username: haozia816  
> Created at: 2020-06-12 09:08:37 UTC  
> Updated at: 2020-06-15 09:41:34 UTC  
> Url: https://github.com/boostorg/process/issues/163  

version: 1.73.0  
source: boost/process/detail/posix/executor.hpp  
line: 283  
compiler: gcc-8.4.0  
target: armv7  
machine: rpi2  
os: Raspbian GNU/Linux 9.8 (stretch)  
debug: qtcreator & gdbserver remote debug  
  
**Cause of crash：**  
when not found "PATH="  
e != nullptr, but *e == nullptr  
  
**Reproduction method：**  
```c++  
#include <unistd.h>  
#include <string>  
#include <iostream>  
using namespace std;  
int main()  
{  
	auto e = ::environ;  
	while ((*e != nullptr) && 0 != std::string(*e).find("PATH="))  
		e++;  
	if (e != nullptr)  
		std::cout << std::string(*e) /* crash here */ << std::endl;  
	return 0;  
}  
```

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2020-06-15 09:41:34 UTC  
> Url: https://github.com/boostorg/process/issues/163#issuecomment-644022006  

This came up with a static sanitizer recently, there is a fix in develop. Will hopefully merge that soon (got a few failing tests to handle first).
