# #220 - basic_managed_shared_memory::grow() do not return when using raw pointers [Closed]

> Username: pliniofpa  
> Created at: 2024-05-16 08:46:44 UTC  
> Updated at: 2024-09-26 12:51:26 UTC  
> Closed at: 2024-09-26 12:51:26 UTC  
> Url: https://github.com/boostorg/interprocess/issues/220  

When trying to grow the shared memory that is using raw pointers (instead of boost::interprocess::offset_ptr) the grow() function blocks and never returns.  
  
How to reproduce:  
  
```  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/shared_memory_object.hpp>  
#include <iostream>  
#include <thread>  
  
namespace bip            = boost::interprocess;  
using shm_raw_ptr_t      = bip::basic_managed_shared_memory<char,bip::rbtree_best_fit< bip::mutex_family, void*>, bip::iset_index>;  
using shm_offset_ptr_t   = bip::basic_managed_shared_memory<char,bip::rbtree_best_fit< bip::mutex_family, bip::offset_ptr<void> >, bip::iset_index>;  
  
int main() {  
    using myShmType = shm_raw_ptr_t;  
    constexpr auto base_address = 0x7fff956e5000;  
    const char* name = "shm_grow_test";    
    bip::shared_memory_object::remove(name);  
    size_t sizeInBytes = 1L*1024;  
    auto shm = myShmType(bip::open_or_create, name, sizeInBytes, (void*)base_address);  
  
    for(int i = 0; i < 10; i++) {  
        sizeInBytes = sizeInBytes * 25L/100L;  
        std::cout << "BEFORE GROW" << std::endl;  
        myShmType::grow(name, sizeInBytes);  
        std::cout << "AFTER GROW" << std::endl;  
        shm = myShmType();  
        shm = myShmType(bip::open_only, name , (void*)base_address);  
        std::this_thread::sleep_for(std::chrono::milliseconds(1000));          
    }  
    return 0;  
}  
  
```  
  
Changing the first line inside main to: `using myShmType = shm_offset_ptr_t;` to create a managed shared memory segment using boost::interprocess::offset_ptr instead of raw pointers will work properly.  
  
  
Environment Information:  
Boost Version: 1.85  
> cat /etc/os-release   
NAME="Oracle Linux Server"  
VERSION="8.5"  
ID="ol"  
ID_LIKE="fedora"  
VARIANT="Server"  
VARIANT_ID="server"  
VERSION_ID="8.5"  
PLATFORM_ID="platform:el8"  
PRETTY_NAME="Oracle Linux Server 8.5"  
ANSI_COLOR="0;31"  
CPE_NAME="cpe:/o:oracle:linux:8:5:server"  
HOME_URL="https://linux.oracle.com/"  
BUG_REPORT_URL="https://bugzilla.oracle.com/"  
> g++ --version  
g++ (GCC) 8.5.0 20210514 (Red Hat 8.5.0-16.0.2)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-09-26 12:50:08 UTC  
> Url: https://github.com/boostorg/interprocess/issues/220#issuecomment-2376863817  

Raw pointers were never supported for "grow", but it was not explicitly documented, sorry. I'll update the documentation for "grow" and add an static assertion if raw pointers are used. Sorry for the confusing documentation and many thanks for the report, as undoubtely it will serve to improve the library.
