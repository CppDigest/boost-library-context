# #208 - Constructing vector of POD using boost interprocess throws exception only in debug mode [Open]

> Username: qiuwei  
> Created at: 2024-01-18 16:22:41 UTC  
> Updated at: 2024-08-08 22:19:47 UTC  
> Url: https://github.com/boostorg/interprocess/issues/208  

I am trying to construct a bip::vector of POD using boost interprocess managed shared memory. The following code compiles and run in release mode, but crashes in debug mode.  
  
```  
#include <cstdint>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/containers/string.hpp>  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/container/vector.hpp>  
#include <iostream>  
  
// Forward declaration of Ringbuffer<65536>  
template <uint32_t Bytes>  
class Ringbuffer;  
  
namespace shared {  
namespace bipc = boost::interprocess;  
using Segment = bipc::managed_shared_memory;  
using Manager = Segment::segment_manager;  
  
template <typename T>  
using Alloc = bipc::allocator<T, Manager>;  
  
template <typename T>  
using Vector = boost::container::vector<T, Alloc<T>>;  
}  // namespace shared  
  
using MsgQueue = Ringbuffer<65536>;  
  
using msg_queue_pool_t = shared::Vector<MsgQueue>;  
  
template <uint32_t Bytes>  
class Ringbuffer {  
   public:  
    struct MsgHeader {  
        uint32_t size;  
        uint32_t userdata;  
    };  
  
   private:  
    struct Block {  
        alignas(64) MsgHeader header;  
    };  
  
    static constexpr uint32_t BLK_CNT = Bytes / sizeof(Block);  
    static_assert(BLK_CNT && !(BLK_CNT & (BLK_CNT - 1)), "BLK_CNT must be a power of 2");  
  
    Block blk[BLK_CNT];  
  
    alignas(64) uint64_t written_idx{0};  
    alignas(64) uint64_t last_key_idx{0};  
    alignas(64) uint64_t writing_idx{0};  
    alignas(64) bool in_use{false};  
};  
  
int main(int argc, char** argv) {  
    auto segment_ptr =  
        std::make_unique<boost::interprocess::managed_shared_memory>(boost::interprocess::create_only, "ShareMemory", 65536 * 20);  
  
    msg_queue_pool_t* ringbuffer_pool =  
        segment_ptr->construct<msg_queue_pool_t>("queue_pool")(5, segment_ptr->get_segment_manager());  
    ringbuffer_pool->resize(10);  
  
    std::cout << "hello world!" << std::endl;  
    return 0;  
}  
```  
  
The error when running in debug mode:   
```  
boost_interprocess_issue: /home/rnd/.xmake/packages/b/boost/1.81.0/9a76068f70604ea18becd4a3064bdac1/include/boost/interprocess/mem_algo/rbtree_best_fit.hpp:698: T* boost::interprocess::rbtree_best_fit<MutexFamily, VoidMutex, MemAlignment>::allocation_command(boost::container::allocation_type, boost::interprocess::rbtree_best_fit<MutexFamily, VoidMutex, MemAlignment>::size_type, boost::interprocess::rbtree_best_fit<MutexFamily, VoidMutex, MemAlignment>::size_type&, T*&) [with T = Ringbuffer<65536>; MutexFamily = boost::interprocess::mutex_family; VoidPointer = boost::interprocess::offset_ptr; long unsigned int MemAlignment = 0; boost::container::allocation_type = unsigned int; boost::interprocess::rbtree_best_fit<MutexFamily, VoidMutex, MemAlignment>::size_type = long unsigned int]: Assertion `0 == ((std::size_t)ret % ::boost::container::dtl::alignment_of::value)' failed.  
```  
  
how could I fix the issue?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-08-08 22:19:46 UTC  
> Url: https://github.com/boostorg/interprocess/issues/208#issuecomment-2276781429  

The problem in this example is that the type is overaligned, which is not supported by Boost.Interprocess allocators. In Debug mode, an assertion triggers when trying to make sure that the memory is aligned for the type being allocated. Maybe it should be a compilation error, as boost::interprocess allocators know the alignment of the type and the alignment that the segment manager can guarantee.
