# #257 - Not able to close a library that work with fiber scheduler algorithm or this_fiber [Open]

> Username: somdutto  
> Created at: 2020-08-20 22:18:49 UTC  
> Updated at: 2020-09-30 05:46:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/257  

I am developing a library which use boost fiber. There  I used this_fiber::get_id(), But as soon as I specify this statement I am not able to close the library. Same happens when I specified "boost::fibers::use_scheduling_algorithm< priority_scheduler >();"  
  
Here is the small test case to recreate the scenario  
liba.so created from bellow code with -fno-unique-symbols enabled  
  
  #include <iostream>  
  #include <boost/fiber/all.hpp>  
  using namespace std;  
  
  int func1(int)  
  {  
    cout << "this fiber id " << boost::this_fiber::get_id() << endl; // if this line is commented out then the problem resolved.  
    cout << "It is a func1" << endl;  
    return 1;  
  }  
----------  
main code from where the library gets loaded  
#include <stdio.h>  
#include <stdlib.h>  
#include <dlfcn.h>  
#include <sys/types.h>  
using namespace std;  
int fun(string & flName)  {  
  void *handle;  
  typedef int (*fn)(int);  
  fn fn1;  
  handle = dlopen(flName.c_str(), RTLD_LOCAL|RTLD_LAZY);  
  fn1 = (fn)dlsym(handle, "_Z5func1i");  
  (*fn1)(2);  
  cout << "handle :" << handle << endl;  
  int ext_code = dlclose(handle);  
  printf ("end exit code %d\n", ext_code);  
}  
  
int main(void)    {  
  std::string fileName("dirpath/liba.so");  
   fun(fileName);  
   fun(fileName);  
   fun(fileName);  
   return 0;  
}  
  
Output.  
output, when this_fiber is commented out  
  
handle : 0x234567  
end exit code 0  
handle :0x345678  
end exit code 0  
handle : 0x456789  
end exit code 0  
----- but when this_fiber::get_id() is active then  
  
handle : 0x234567  
end exit code 0  
handle :0x234567  
end exit code 0  
handle : 0x234567  
end exit code 0  
  
How can I deal with this scenario so that dlclose can successfully close the library and release the library handle?

---

## Comment 1

> Username: olk  
> Created at: 2020-09-30 05:45:53 UTC  
> Updated at: 2020-09-30 05:46:16 UTC  
> Url: https://github.com/boostorg/fiber/issues/257#issuecomment-701169599  

Probably due the fact that a static var is used to store the active fiber.
