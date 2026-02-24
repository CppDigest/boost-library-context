# #145 - from_dump unable to load safe_dump_to file [Closed]

> Username: syamajala  
> Created at: 2023-10-17 14:21:46 UTC  
> Updated at: 2024-09-12 08:10:32 UTC  
> Closed at: 2024-09-12 08:10:32 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/145  

I don't seem to be able to read a stacktrace file written by one process using another process. Here is a simple example:  
  
write.cc:  
```  
#include <boost/stacktrace/safe_dump_to.hpp>  
  
int nested_func(int c)  
{  
  boost::stacktrace::safe_dump_to("stack.bin");  
  return c + 1;  
}  
  
int func(int b)  
{  
  return nested_func(b + 1);  
}  
  
int main()  
{  
  func(777);  
}  
```  
  
read.cc:  
```  
#include <boost/stacktrace/stacktrace.hpp>  
#include <iostream>  
#include <fstream>  
  
int main()  
{  
  std::ifstream fs("stack.bin", std::ios::binary);  
  
  const auto st = boost::stacktrace::basic_stacktrace<>::from_dump(fs);  
  std::cout << st << std::endl;  
}  
```  
  
This results in the following:  
```  
 0# 0x0000564109D1F1B5  
 1# 0x0000564109D1F1DA  
 2# 0x0000564109D1F1EA  
 3# 0x00007FC002620CD0  
 4# 0x00007FC002620D8A  
 5# 0x0000564109D1F0B5  
```  
  
If I read stack.bin from write.cc though it seems to work correctly.   
  
Both read.cc and write.cc where built the following way:  
```  
g++ -DBOOST_STACKTRACE_USE_BACKTRACE -lbacktrace -ldl -g -o read read.cc  
g++ -DBOOST_STACKTRACE_USE_BACKTRACE -lbacktrace -ldl -g -o write write.cc  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-02-19 08:24:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/145#issuecomment-1951927217  

Yes, this is due to PIC/PIE. I should provide a patch like https://github.com/boostorg/stacktrace/commit/fbcd543b5180b8ee307fef6fbfc34aa372d330cf to make it work for position independent code. This would take quite some time...

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-09-12 08:10:32 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/145#issuecomment-2345559030  

Looks like someone is willing to work on this issue in #180   
  
Closing this ticket as a duplicate
