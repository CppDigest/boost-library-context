# #246 - Much higher memory usage using Boost Interprocess [Closed]

> Username: diehard2  
> Created at: 2024-05-05 15:15:39 UTC  
> Updated at: 2024-06-08 07:19:20 UTC  
> Closed at: 2024-06-08 07:19:20 UTC  
> Url: https://github.com/boostorg/unordered/issues/246  

We just upgraded to boost 1.84, so I'm able to use the new flat maps with boost interprocess (thanks for that!). I'm seeing bad_alloc's in boost interprocess at a surprisingly low number of insertions. For the following code and boost::unordered_map I can set reserved_capacity to 6100 before failures. Changing to flat_map I can only insert 950. I know this uses more memory, but 6.5x feels extremely high  
  
```  
  
#include <boost/unordered_map.hpp>  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <string>  
#include <string_view>  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/containers/string.hpp>  
#include <boost/interprocess/sync/named_upgradable_mutex.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <boost/interprocess/sync/sharable_lock.hpp>  
  
namespace bip = boost::interprocess;  
  
using shared_string =  
    bip::basic_string<char, std::char_traits<char>, bip::allocator<char, bip::managed_shared_memory::segment_manager>>;  
using Segment = bip::managed_shared_memory;  
using SegmentManager = Segment::segment_manager;  
using Allocator = bip::allocator<void, SegmentManager>;  
using SharedStringPair = std::pair<shared_string, shared_string>;  
using SharedStringPairAllocator = bip::allocator<SharedStringPair, SegmentManager>;  
using segment_manager_t = bip::managed_shared_memory::segment_manager;  
using CharAllocator = bip::allocator<char, segment_manager_t>;  
  
namespace std {  
  template <>  
  struct hash<shared_string>  
  {  
    size_t operator()(const shared_string& s) const  
    {  
      return hash<std::string>{}(std::string(s.data(), s.size()));  
    }  
  };  
}  // namespace std  
  
using shared_unordered_map = boost::unordered_flat_map<shared_string, shared_string, std::hash<shared_string>,  
                                                       std::equal_to<>, SharedStringPairAllocator>;  
  
int main(int argc, char* argv[])  
{  
  try {  
    bip::shared_memory_object::remove("SharedMemorySegment");  
    bip::managed_shared_memory segment(bip::create_only, "SharedMemorySegment", 65530);  
    bip::shared_memory_object::remove("SharedMemorySegment");  
  
    const std::size_t reserved_capacity = 1;  // Reserve space for 1000 elements  
    auto* foo =  
        segment.construct<shared_unordered_map>("SharedUnorderedMap")(reserved_capacity, segment.get_segment_manager());  
    
    segment.destroy_ptr(foo);  
    bip::shared_memory_object::remove("SharedMemorySegment");  
 } catch (const std::bad_alloc& ex) {  
   std::cout << "alloc failed"  
   return EXIT_FAILURE;  
 }  
  
   return EXIT_SUCCESS;  
 }  
```

---

## Comment 1

> Username: pdimov  
> Created at: 2024-05-05 15:23:26 UTC  
> Url: https://github.com/boostorg/unordered/issues/246#issuecomment-2094849732  

You're not doing any actual insertions though.  
  
With `unordered_map`, reserving only reserves space for pointers to elements, it doesn't allocate any storage for the actual elements to be inserted. For `unordered_flat_map`, reserving reserves space for the elements as well.  
  
If you change the code to also insert elements after the reservation, I'd expect the flat map to be able to hold slightly more.

---

## Comment 2

> Username: diehard2  
> Created at: 2024-05-05 18:22:44 UTC  
> Url: https://github.com/boostorg/unordered/issues/246#issuecomment-2094903180  

Sorry for the impreciseness of my question. I dug a little deeper into the insertions between unordered_flat_map and unordered_map and I'm still seeing a significant difference before failure (although less than in the original post). Not sure if this is expected or not, and apologies if I'm doing something incorrectly here  
  
**boost::unordered_map**  
bytes allocated- 0.06 MB | inserted elements - 742 | total pair size 0.04 MB(MB)  
bytes allocated- 0.63 MB | inserted elements - 7005 | total pair size 0.35 MB(MB)  
bytes allocated- 6.50 MB | inserted elements - 74739 | total pair size 3.71 MB(MB)  
bytes allocated- 65.00 MB | inserted elements - 768397 | total pair size 38.11 MB(MB)  
bytes allocated- 650.00 MB | inserted elements - 7182731 | total pair size 356.20 MB(MB)  
  
**boost::unordered_flat**  
bytes allocated- 0.06 MB |  inserted elements - 419 | total pair size 0.02 MB(MB)  
bytes allocated- 0.63 MB |  inserted elements - 3359 | total pair size 0.17 MB(MB)  
bytes allocated- 6.50 MB |  inserted elements - 26879 | total pair size 1.33 MB(MB)  
bytes allocated- 65.00 MB |  inserted elements - 430079 | total pair size 21.33 MB(MB)  
bytes allocated- 650.00 MB |  inserted elements - 3440639 | total pair size 170.62 MB(MB)  
  
and the runner code  
  
```   
#include <boost/unordered_map.hpp>  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <cstdint>  
#include <cstdlib>  
#include <exception>  
#include <random>  
#include <string>  
#include <string_view>  
#include "logging_helper.h"  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/containers/string.hpp>  
#include <boost/interprocess/sync/named_upgradable_mutex.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <boost/interprocess/sync/sharable_lock.hpp>  
  
namespace bip = boost::interprocess;  
  
using shared_string =  
    bip::basic_string<char, std::char_traits<char>, bip::allocator<char, bip::managed_shared_memory::segment_manager>>;  
using Segment = bip::managed_shared_memory;  
using SegmentManager = Segment::segment_manager;  
using Allocator = bip::allocator<void, SegmentManager>;  
using SharedStringPair = std::pair<shared_string, shared_string>;  
using SharedStringPairAllocator = bip::allocator<SharedStringPair, SegmentManager>;  
using segment_manager_t = bip::managed_shared_memory::segment_manager;  
using CharAllocator = bip::allocator<char, segment_manager_t>;  
  
namespace std {  
  template <>  
  struct hash<shared_string>  
  {  
    size_t operator()(const shared_string& s) const  
    {  
      return hash<std::string>{}(std::string(s.data(), s.size()));  
    }  
  };  
}  // namespace std  
  
using shared_unordered_map = boost::unordered_map<shared_string, shared_string, std::hash<shared_string>,  
                                                  std::equal_to<>, SharedStringPairAllocator>;  
  
using shared_unordered_flat_map = boost::unordered_flat_map<shared_string, shared_string, std::hash<shared_string>,  
                                                            std::equal_to<>, SharedStringPairAllocator>;  
  
std::string random_string(size_t length)  
{  
  static const char characters[] = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";  
  std::string result(length, 0);  
  static std::random_device rd;  
  static std::mt19937 gen(rd());  
  std::uniform_int_distribution<> dis(0, sizeof(characters) - 2);  
  
  for (size_t i = 0; i < length; ++i) {  
    result[i] = characters[dis(gen)];  
  }  
  
  return result;  
}  
  
std::string formatBytesToMegabytes(std::uint64_t bytes)  
{  
  double megabytes = static_cast<double>(bytes) / (1024.0 * 1024.0);  
  return std::format("{:.2f} MB", megabytes);  
}  
  
// Notes - unordered 6100 elements  
//         unordered_flat 950 elements  
  
int main(int argc, char* argv[])  
{  
  std::array<std::uint64_t, 5> byteSizes = {  
      65 * 1024ull,                                   // 65 KB in bytes  
      650 * 1024ull,                                  // 650 KB in bytes  
      static_cast<std::uint64_t>(6.5 * 1024 * 1024),  // 6.5 MB in bytes  
      65 * 1024ull * 1024,                            // 65 MB in bytes  
      650 * 1024ull * 1024                            // 650 MB in bytes  
  };  
  
  uint64_t size_of_pair = 52;  
  auto* loggerp = InitializeLogger("symbology_cache", "INFO");  
  fds::FDSExceptionInit(fds::stacktrace::STACK_METHOD::RAW, false);  
  
  for (auto size : byteSizes) {  
    bip::shared_memory_object::remove("SharedMemorySegment");  
    bip::managed_shared_memory segment(bip::create_only, "SharedMemorySegment", size);  
    bip::shared_memory_object::remove("SharedMemorySegment");  
  
    size_t N = 0;                  // Number of random items to insert  
    const size_t key_length = 10;  // Length of the random key  
  
    auto* foo = segment.construct<shared_unordered_map>("SharedUnorderedMap")(segment.get_segment_manager());  
  
    try {  
      while (true) {  
        shared_string key(random_string(key_length).c_str(), CharAllocator(segment.get_segment_manager()));  
        shared_string value(random_string(key_length).c_str(), CharAllocator(segment.get_segment_manager()));  
        foo->insert(std::make_pair(key, value));  
        N++;  
      }  
    } catch (...) {  
      std::cout << std::format("bytes allocated - {} | inserted elements - {} | total pair size {}(MB)\n",  
                               formatBytesToMegabytes(size), N, formatBytesToMegabytes(N * size_of_pair));  
      segment.destroy_ptr(foo);  
      bip::shared_memory_object::remove("SharedMemorySegment");  
    }  
  }  
  
  std::cout << "\n\n\n";  
  
  for (auto size : byteSizes) {  
    bip::shared_memory_object::remove("SharedMemorySegment");  
    bip::managed_shared_memory segment(bip::create_only, "SharedMemorySegment", size);  
    bip::shared_memory_object::remove("SharedMemorySegment");  
  
    size_t N = 0;                  // Number of random items to insert  
    const size_t key_length = 10;  // Length of the random key  
  
    auto* foo = segment.construct<shared_unordered_flat_map>("SharedUnorderedMap")(segment.get_segment_manager());  
  
    try {  
      while (true) {  
        shared_string key(random_string(key_length).c_str(), CharAllocator(segment.get_segment_manager()));  
        shared_string value(random_string(key_length).c_str(), CharAllocator(segment.get_segment_manager()));  
        foo->insert(std::make_pair(key, value));  
        N++;  
      }  
    } catch (...) {  
      std::cout << std::format("bytes allocated - {} |  inserted elements - {} | total pair size {}(MB)\n",  
                               formatBytesToMegabytes(size), N, formatBytesToMegabytes(N * size_of_pair));  
      segment.destroy_ptr(foo);  
      bip::shared_memory_object::remove("SharedMemorySegment");  
    }  
  }  
  
  return EXIT_SUCCESS;  
}  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2024-05-05 19:16:59 UTC  
> Url: https://github.com/boostorg/unordered/issues/246#issuecomment-2094918332  

I ran the `string.cpp` benchmark and the results are indeed that `boost::unordered_flat_map` consumes significantly more memory than `boost::unordered_map`:  
  
```  
                  std::unordered_map: 34979 ms, 291081368 bytes in 3999510 allocations  
                boost::unordered_map: 28361 ms, 245453904 bytes in 3999510 allocations  
           boost::unordered_node_map: 16808 ms, 231283496 bytes in 3999509 allocations  
           boost::unordered_flat_map: 14856 ms, 322961400 bytes in 1 allocations  
```  
You should try `unordered_node_map` and see if it fits your purposes better, as it's competitive with `unordered_map` in memory use, while still being significantly faster.

---

## Comment 4

> Username: diehard2  
> Created at: 2024-05-05 23:29:19 UTC  
> Url: https://github.com/boostorg/unordered/issues/246#issuecomment-2094993386  

Thanks for looking into this. I did some more benchmarking, and I think I'll take the memory hit for unordered_flat_map. My benchmarks are showing unordered_node_map is faster on insertion, but slower on a large number of reads. unordered_flat_map was the winner. Not sure if this is expected, but these were the numbers I saw. They were relatively insensitive to as long as the number of items in the map were within the same order of magnitude  
  
Adding the below information for anyone who is curious -  
  
```  
unordered map - bytes allocated - 650.00 MB | inserted elements - 7182731 | total pair size 356.20 MB(MB) | time taken: 5897 ms | read 200k elements 41 ms  
  
unordered flat map - bytes allocated - 650.00 MB | inserted elements - 3440639 | total pair size 170.62 MB(MB) | time taken: 1179 ms | read 200k elements 30 ms  
  
unordered node - bytes allocated - 650.00 MB | inserted elements - 6881279 | total pair size 341.25 MB(MB) | time taken: 3727 ms | read 200k elements 53 ms  
```  
  
  
same number of keys in each map  
  
```  
******** boost unordered ********  
bytes allocated - 650.00 MB | inserted elements - 3440639 | total pair size 170.62 MB(MB) | time taken: 2695 ms | read 100k elements 39 ms  
  
******** boost unordered flat ********  
bytes allocated - 650.00 MB | inserted elements - 3440639 | total pair size 170.62 MB(MB) | time taken: 1132 ms | read 100k elements 30 ms  
  
******** boost unordered node ********  
bytes allocated - 650.00 MB | inserted elements - 3440639 | total pair size 170.62 MB(MB) | time taken: 1701 ms | read 100k elements 50 ms  
```  
  
In summary :  
  
Insertion flat_map > unordered_node_map > unordered_map  
  
Large reads - flat_map > unordered_map > unordered_node_map  
  
Runner code:  
  
```   
  
  
#include <boost/unordered_map.hpp>  
#include <boost/unordered/unordered_node_map.hpp>  
#include <boost/unordered/unordered_flat_map.hpp>  
#include <cstdint>  
#include <cstdlib>  
#include <exception>  
#include <random>  
#include <string>  
#include <string_view>  
#include "logging_helper.h"  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/containers/string.hpp>  
#include <boost/interprocess/sync/named_upgradable_mutex.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <boost/interprocess/sync/sharable_lock.hpp>  
  
  
namespace bip = boost::interprocess;  
  
using shared_string =  
    bip::basic_string<char, std::char_traits<char>, bip::allocator<char, bip::managed_shared_memory::segment_manager>>;  
using Segment = bip::managed_shared_memory;  
using SegmentManager = Segment::segment_manager;  
using Allocator = bip::allocator<void, SegmentManager>;  
using SharedStringPair = std::pair<shared_string, shared_string>;  
using SharedStringPairAllocator = bip::allocator<SharedStringPair, SegmentManager>;  
using segment_manager_t = bip::managed_shared_memory::segment_manager;  
using CharAllocator = bip::allocator<char, segment_manager_t>;  
  
namespace std {  
  template <>  
  struct hash<shared_string>  
  {  
    size_t operator()(const shared_string& s) const  
    {  
      return hash<std::string>{}(std::string(s.data(), s.size()));  
    }  
  };  
}  // namespace std  
  
using shared_std_unordered_map = std::unordered_map<shared_string, shared_string, std::hash<shared_string>,  
                                                    std::equal_to<>, SharedStringPairAllocator>;  
  
using shared_unordered_map = boost::unordered_map<shared_string, shared_string, std::hash<shared_string>,  
                                                  std::equal_to<>, SharedStringPairAllocator>;  
  
using shared_unordered_flat_map = boost::unordered_flat_map<shared_string, shared_string, std::hash<shared_string>,  
                                                            std::equal_to<>, SharedStringPairAllocator>;  
  
using shared_unordered_node_map = boost::unordered_node_map<shared_string, shared_string, std::hash<shared_string>,  
                                                            std::equal_to<>, SharedStringPairAllocator>;  
  
std::string random_string(size_t length)  
{  
  static const char characters[] = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";  
  std::string result(length, 0);  
  static std::random_device rd;  
  static std::mt19937 gen(rd());  
  std::uniform_int_distribution<> dis(0, sizeof(characters) - 2);  
  
  for (size_t i = 0; i < length; ++i) {  
    result[i] = characters[dis(gen)];  
  }  
  
  return result;  
}  
  
std::string formatBytesToMegabytes(std::uint64_t bytes)  
{  
  double megabytes = static_cast<double>(bytes) / (1024.0 * 1024.0);  
  return std::format("{:.2f} MB", megabytes);  
}  
  
// Notes - unordered 6100 elements  
//         unordered_flat 950 elements  
  
template <typename T>  
void measure(const std::string& name)  
{  
  std::cout << "******** " << name << " ********" << std::endl;  
  constexpr std::array<std::uint64_t, 5> byteSizes = {  
      65 * 1024ull,                                   // 65 KB in bytes  
      650 * 1024ull,                                  // 650 KB in bytes  
      static_cast<std::uint64_t>(6.5 * 1024 * 1024),  // 6.5 MB in bytes  
      65 * 1024ull * 1024,                            // 65 MB in bytes  
      650 * 1024ull * 1024                            // 650 MB in bytes  
  };  
  uint64_t size_of_pair = 52;  
  
  for (auto size : byteSizes) {  
    bip::shared_memory_object::remove("SharedMemorySegment");  
    bip::managed_shared_memory segment(bip::create_only, "SharedMemorySegment", size);  
    bip::shared_memory_object::remove("SharedMemorySegment");  
  
    size_t N = 0;                  // Number of random items to insert  
    const size_t key_length = 10;  // Length of the random key  
  
    auto* foo = segment.construct<T>("SharedUnorderedMap")(segment.get_segment_manager());  
    auto start_time = std::chrono::high_resolution_clock::now();  
    try {  
      while (true) {  
        shared_string key(random_string(key_length).c_str(), CharAllocator(segment.get_segment_manager()));  
        shared_string value(random_string(key_length).c_str(), CharAllocator(segment.get_segment_manager()));  
        foo->emplace(std::move(key), std::move(value));  
        N++;  
      }  
    } catch (...) {  
      auto end_time = std::chrono::high_resolution_clock::now();  
      auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(end_time - start_time).count();  
  
      long read_duration = 0;  
      if (N >= 200000) {  
        // Create a vector of iterators  
        std::vector<typename T::iterator> iterators;  
        iterators.reserve(N);  
        for (auto it = foo->begin(); it != foo->end(); ++it) {  
          iterators.push_back(it);  
        }  
  
        // Shuffle the iterators  
        std::random_device rd;  
        std::mt19937 g(rd());  
        std::shuffle(iterators.begin(), iterators.end(), g);  
  
        auto start_read_time = std::chrono::high_resolution_clock::now();  
        for (int i = 0; i < 200000; i++) {  
          auto iter = foo->find(iterators[i]->first);  
        }  
  
        auto end_read_time = std::chrono::high_resolution_clock::now();  
        read_duration = std::chrono::duration_cast<std::chrono::milliseconds>(end_read_time - start_read_time).count();  
      }  
  
      std::cout << std::format(  
          "bytes allocated - {} | inserted elements - {} | total pair size {}(MB) | time taken: {} ms | read 200k "  
          "elements {}\n",  
          formatBytesToMegabytes(size), N, formatBytesToMegabytes(N * size_of_pair), duration, read_duration);  
      segment.destroy_ptr(foo);  
      bip::shared_memory_object::remove("SharedMemorySegment");  
    }  
  }  
  
  std::cout << "\n\n\n";  
}  
  
int main(int argc, char* argv[])  
{  
  // auto* loggerp = InitializeLogger("symbology_cache", "INFO");  
  fds::FDSExceptionInit(fds::stacktrace::STACK_METHOD::RAW, false);  
  
  //  measure<shared_std_unordered_map>("std unodered");  
  measure<shared_unordered_map>("boost unordered");  
  measure<shared_unordered_flat_map>("boost unordered flat");  
  measure<shared_unordered_node_map>("boost unordered node");  
  
  return EXIT_SUCCESS;  
}  
```
