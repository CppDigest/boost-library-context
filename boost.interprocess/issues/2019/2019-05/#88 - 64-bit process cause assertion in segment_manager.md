# #88 - 64-bit process cause assertion in segment_manager [Closed]

> Username: cngzhnp  
> Created at: 2019-05-22 13:45:10 UTC  
> Updated at: 2026-01-07 09:36:11 UTC  
> Closed at: 2026-01-07 09:36:11 UTC  
> Url: https://github.com/boostorg/interprocess/issues/88  

In our scenario, there are two processes(one side 32-bit and other one 64-bit) communicate with each other over shared memory.  
  
Boost Version : 1.70.0   
  
Compiler Version : gcc 7.4.0  
  
Here is the implementation code for 64-bit side as like that:  
  
```  
#define QUEUE_CAPACITY 5  
  
namespace bi = boost::interprocess;  
  
using adn_linux_ptr_t = bi::offset_ptr<void, std::int32_t, std::uint64_t, sizeof(std::uint64_t)>;  
  
using adn_linux_external_buf_t = bi::basic_managed_external_buffer<char, bi::rbtree_best_fit<bi::null_mutex_family, adn_linux_ptr_t, sizeof(std::uintptr_t)>, bi::iset_index>;  
  
namespace shm  
{  
   using handle = adn_linux_external_buf_t::handle_t;  
   using ring_buffer = boost::lockfree::spsc_queue<handle, boost::lockfree::capacity<QUEUE_CAPACITY>>;  
}  
  
int main(int argc, char argv[])  
{  
    adn_linux_external_buf_t segment(bi::open_only, shared_mem_addr, SEGMENT_SIZE);  
  
    std::cout << "Segment is created " << std::hex << addressof(segment) << std::endl;  
  
    auto seg_ptr = segment.get_segment_manager();  
    if(nullptr == seg_ptr)  
    {  
       std::cout << "Segment manager failed" << std::endl;  
       return 0;  
    }  
  
    size_t free_size = segment.get_free_memory();  
    if(0 == free_size)  
    {  
       std::cout << "No free memory available!" << std::endl;  
       return 0;  
    }  
  
    if(!segment.check_sanity())  
    {  
       std::cout << "Check sanity failed!" << std::endl;  
       return 0;  
    }  
    std::cout << "Free available memory size: " << free_size << std::endl;  
  
    auto f_ping_queue = segment.find<shm::ring_buffer>("ping_queue");  
    auto f_pong_queue = segment.find<shm::ring_buffer>("pong_queue");  
  
    shm::ring_buffer *ping_queue = f_ping_queue.first;  
    shm::ring_buffer *pong_queue = f_pong_queue.first;  
  
    if(ping_queue == nullptr || pong_queue == nullptr)  
    {  
        std::cout << "One or more queue could not be created!" << std::endl;  
        return 0;  
    }  
}  
```  
  
In 32-bit side, implementation can be represented like that:  
  
```  
namespace bi = boost::interprocess;  
  
using adn_linux_ptr_t = bi::offset_ptr<void, std::int32_t, std::uint64_t, sizeof(std::uint64_t)>;  
    
using adn_linux_external_buf_t = bi::basic_managed_external_buffer<char, bi::rbtree_best_fit<bi::null_mutex_family, adn_linux_ptr_t, sizeof(boost::uint64_t)>, bi::iset_index>;  
    
namespace shm  
{  
  using handle = adn_linux_external_buf_t::handle_t;   
    
  using ring_buffer = boost::lockfree::spsc_queue<handle, boost::lockfree::capacity<QUEUE_CAPACITY> >;  
}  
  
int main(int argc, char *argv[])  
{  
    adn_linux_external_buf_t segment(bi::create_only, shared_mem_addr, SEGMENT_SIZE);  
      
    size_t free_size = segment.get_free_memory();  
  
    if(0 == free_size)  
    {  
        std::cout << "No memory available!\n";  
        return;  
    }  
  
    if(!segment.check_sanity())  
    {  
        std::cout << "Check sanity failed!\n";  
        return;  
    }  
  
    std::cout << "Free memory size is : " << free_size << std::endl;  
  
    shm::ring_buffer *f_ping_queue = segment.construct<shm::ring_buffer>("ping_queue")();   
    shm::ring_buffer *f_pong_queue = segment.construct<shm::ring_buffer>("pong_queue")();  
  
    if(f_ping_queue == 0 || f_pong_queue == 0)   
    {  
        std::cout << "Queues could not be created\n";  
        return;  
    }   
  
   std::cout << "Queues are available\n";  
  
   adn_linux_external_buf_t::handle_t handle = 0;  
  
   return 0;  
}  
```  
  
For communication to each other, we just adjust offset_ptr template on both side but we are getting assertion from 64-bit process in file segment_manager.hpp:861 as you can see below:  
  
`consumer: ./boost/interprocess/segment_manager.hpp:861: void* boost::interprocess::segment_manager<CharType, MemoryAlgorithm, IndexType>::priv_generic_find(const CharT*, IndexType<boost::interprocess::ipcdetail::index_config<CharT, MemoryAlgorithm> >&, boost::interprocess::ipcdetail::in_place_interface&, boost::interprocess::segment_manager<CharType, MemoryAlgorithm, IndexType>::size_type&, boost::interprocess::ipcdetail::true_, bool) [with CharT = char; CharType = char; MemoryAlgorithm = boost::interprocess::rbtree_best_fit<boost::interprocess::null_mutex_family, boost::interprocess::offset_ptr<void, int, long unsigned int, 8>, 8>; IndexType = boost::interprocess::iset_index; boost::interprocess::segment_manager<CharType, MemoryAlgorithm, IndexType>::size_type = unsigned int; boost::interprocess::ipcdetail::true_ = boost::interprocess::ipcdetail::bool_<true>]: Assertion (ctrl_data->m_value_bytes % table.size) == 0' failed.  
`  
  
**PS: shared_mem_addr is somewhere which is mmap from the system as a shared memory where both processes can have access on it.**  
  
So, as far as I can see from Boost issue tracker, offset_ptr should be re-defined for our purpose as above. However, problem is still occurred.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2026-01-07 09:36:11 UTC  
> Url: https://github.com/boostorg/interprocess/issues/88#issuecomment-3718031303  

Closing old bugs
