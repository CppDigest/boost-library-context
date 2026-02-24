# #140 - deleter raise [Assertion `hdr->m_value_alignment == algn' failed] when deleting object [Closed]

> Username: liuzqt  
> Created at: 2021-02-23 02:03:01 UTC  
> Updated at: 2021-04-21 12:31:22 UTC  
> Closed at: 2021-04-21 12:31:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/140  

I allocate some memory from `managed_shared_memory` and use placement new to construct a object in given address. Then I create a `ipc::shared_ptr` to manage the lifecycle of this object.   
When the shared_ptr destruct, it raise exception when trying to release the obj pointer  
  
```  
tmp: /home/ziqi.liu/.tspkg/include/boost/interprocess/detail/segment_manager_helper.hpp:182: static boost::interprocess::ipcdetail::block_header<size_type>* boost::interprocess::ipcdetail::block_header<size_type>::block_header_from_value(const void*, std::size_t, std::size_t) [with size_type = long unsigned int; std::size_t = long unsigned int]: Assertion `hdr->m_value_alignment == algn' failed.  
```  
  
  
code to reproduce the problem:  
```  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/containers/vector.hpp>  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/smart_ptr/deleter.hpp>  
#include <boost/interprocess/smart_ptr/shared_ptr.hpp>  
#include <cassert>  
#include <iostream>  
#include <memory>  
  
using namespace boost::interprocess;  
  
typedef managed_shared_memory::segment_manager segment_manager_type;  
  
// This is type of the object we want to share  
class MyType {  
 public:  
  ~MyType() { std::cout << "destroy MyType" << std::endl; }  
};  
  
typedef allocator<void, segment_manager_type> void_allocator_type;  
typedef deleter<MyType, segment_manager_type> deleter_type;  
typedef shared_ptr<MyType, void_allocator_type, deleter_type> my_shared_ptr;  
  
int main() {  
  managed_shared_memory segment(open_or_create, "MySharedMemory", 4096 * 10);  
  
  auto alloc_inst = segment.get_allocator<MyType>();  
  auto ptr = alloc_inst.allocate(1);  
  MyType *obj = new (&*ptr) MyType();  
  my_shared_ptr s_ptr = make_managed_shared_ptr<MyType>(obj, segment);  
  
  return 0;  
}  
```  
  
I believe I must have done something wrong....but I can not figure it out after reading the docuemnts. Please help, thanks!

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-04-21 12:31:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/140#issuecomment-824022803  

Hi, you are allocating raw memory instead of an object and shared_pointer needs an properly constructed shared memory object. Instead of allocating raw memory and doing placement new, try:  
  
```  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/containers/vector.hpp>  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/smart_ptr/deleter.hpp>  
#include <boost/interprocess/smart_ptr/shared_ptr.hpp>  
#include <cassert>  
#include <iostream>  
#include <memory>  
  
using namespace boost::interprocess;  
  
typedef managed_shared_memory::segment_manager segment_manager_type;  
  
// This is type of the object we want to share  
class MyType {  
 public:  
  ~MyType() { std::cout << "destroy MyType" << std::endl; }  
};  
  
typedef allocator<void, segment_manager_type> void_allocator_type;  
typedef deleter<MyType, segment_manager_type> deleter_type;  
typedef shared_ptr<MyType, void_allocator_type, deleter_type> my_shared_ptr;  
  
int main() {  
  managed_shared_memory segment(open_or_create, "MySharedMemory", 4096 * 10);  
  MyType *obj = segment.construct<MyType>(anonymous_instance)();  
  
  my_shared_ptr s_ptr = make_managed_shared_ptr<MyType>(obj, segment);  
  return 0;  
}  
```
