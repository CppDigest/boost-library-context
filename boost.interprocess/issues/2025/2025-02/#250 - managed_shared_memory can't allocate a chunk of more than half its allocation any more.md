# #250 - managed_shared_memory can't allocate a chunk of more than half its allocation any more [Open]

> Username: JulesRapanga  
> Created at: 2025-02-10 16:44:22 UTC  
> Updated at: 2025-02-10 16:44:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/250  

```cpp  
#include <unistd.h>  
#include <iostream>  
#include <boost/interprocess/managed_shared_memory.hpp>  
  
using namespace boost::interprocess;  
  
#define HEADSPACE (1 << 10)  
const size_t SIZE = 20'000'000;  
  
struct Header {  
    managed_shared_memory::handle_t buf = {};  
    unsigned size = 0;  
    unsigned counter = 0;  
};  
  
int main()  
{  
    shared_memory_object::remove("Paper name");  
  
    size_t size = SIZE;  
    size_t paper_size = (sizeof(Header) + HEADSPACE + size);  
    std::cerr << getpid() << " Making paper: total_size: " << paper_size << " req size: " << size << " HEADSPACE: " << (HEADSPACE) << std::endl;  
    managed_shared_memory shm(open_or_create, "Paper name", paper_size);  
    Header* h = shm.find_or_construct<Header>("Header")();  
    void * buf = shm.allocate_aligned(size, 32);  
    h->buf = shm.get_handle_from_address(buf);  
    h->size = size;  
}  
```  
  
Above is a roughly cut down version of some production code I'm running; for a given memory size, allocate create a `managed_shared_memory` of a slightly larger size, construct a `Header` in it, and allocate the desired size as an aligned buffer (stored in the header) . In 1.86.0 and before it successfully allocates all the memory, but in 1.87.0 it throws `bad_alloc`. Some investigation shows that the `allocated_aligned` step only succeeds if the size is <=1/2 of the desired size, and that allocating smaller chunks does allow access to most of the allocated memory (i.e. you can ask for 1/10 of size 9 times but the 10th fails).
