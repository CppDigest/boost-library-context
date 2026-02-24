# #194 - Extremly slow performance when constructing large arrays of basic type [Open]

> Username: ax7e  
> Created at: 2023-03-10 07:11:50 UTC  
> Updated at: 2023-03-13 03:21:35 UTC  
> Url: https://github.com/boostorg/interprocess/issues/194  

Hi, there are several Stack Overflow questions regarding performance issues when creating large arrays of basic types. For example, in this question (https://stackoverflow.com/questions/74609901/how-create-a-big-array-in-shared-memory-with-boostinterprocessmanaged-shard), the poster also experienced this problem. After profiling the code, I noticed that the root cause of the problem seems to be in this section (https://github.com/boostorg/interprocess/blob/55ad499dc4f9d1687dde25aeb23b0c6bffd44ed7/include/boost/interprocess/detail/named_proxy.hpp#L67-L76), which attempts to call the constructor for every element in the array.  
  
Is it possible to write code to handle cases where the array is composed of basic types, and then initialize it using memset?  
  
## To reproduce  
```c++  
#include <boost/interprocess/managed_shared_memory.hpp>  
namespace bip = boost::interprocess;  
  
int main() {  
    auto   id_   = "shmTest";  
    size_t size_ = 2ul << 30;  
  
    bip::shared_memory_object::remove(id_);  
    bip::managed_shared_memory sm(bip::create_only, id_, size_ + 1024);  
  
    auto data_ = sm.construct<char>("Data")[size_]('\0');  
}  
```
