# #228 - Assertion failed: ((ctrl_data->m_value_bytes % table.size) == 0), function priv_generic_find [Closed]

> Username: Quuxplusone  
> Created at: 2024-08-22 20:48:16 UTC  
> Updated at: 2024-09-26 14:29:23 UTC  
> Closed at: 2024-09-26 11:59:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/228  

This might be a duplicate of #88, but just in case, I'm opening this separate issue for it.  
Here is a complete repro, at least on my M1 MacBook with Boost 1.85.0.  
```  
cat >test.cpp <<EOF  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/managed_shared_memory.hpp>  
#include <cstdio>  
#include <cstdlib>  
#include <vector>  
  
// This is the name of the shared memory segment Boost will create for us.  
// On Posix systems, it's likely a disk file named "/tmp/boost_interprocess/PICKLE".  
#define SHM_SEGMENT_NAME "PICKLE"  
  
namespace bip = boost::interprocess;  
  
namespace shm {  
    template<class T> using allocator = bip::allocator<T, bip::managed_shared_memory::segment_manager>;  
    template<class T> using vector = std::vector<T, shm::allocator<T>>;  
}  
  
int main(int argc, char **argv) {  
  if (argc >= 3 && std::string_view(argv[1]) == "--pickle") {  
    bip::shared_memory_object::remove(SHM_SEGMENT_NAME);  
    auto segment = bip::managed_shared_memory(bip::create_only, SHM_SEGMENT_NAME, 10'000);  
    shm::vector<int> *p = segment.construct<shm::vector<int>>("MY_INTS")(segment.get_segment_manager());  
    for (int i = 2; i < argc; ++i) {  
      p->push_back(atoi(argv[i]));  
    }  
    printf("Wrote %zu elements to a vector in the shared memory segment (vector at %p, data at %p)\n", p->size(), p, p->data());  
  } else if (argc == 2 && std::string_view(argv[1]) == "--unpickle") {  
    auto segment = bip::managed_shared_memory(bip::open_only, SHM_SEGMENT_NAME);  
    auto [p, nelem] = segment.find<std::vector<int>>("MY_INTS");  
    assert(p != nullptr); // it was found  
    assert(nelem == 1);   // and it's a single vector<int> object  
    printf("Found a vector with %zu elements in the shared memory segment (vector at %p, data at %p)\n", p->size(), p, p->data());  
    printf("Those elements are:\n");  
    for (int i : *p) {  
      printf("  %d\n", i);  
    }  
  } else {  
    puts("Usage:");  
    puts("  ./a.out --pickle 1 2 3");  
    puts("  ./a.out --unpickle");  
  }  
}  
EOF  
g++ -I/opt/homebrew/Cellar/boost/1.85.0/include -std=c++17 -W -Wall -O2 test.cpp  
./a.out --pickle 1 2 3  
./a.out --unpickle  
```  
The last command fails with:  
```  
Assertion failed: ((ctrl_data->m_value_bytes % table.size) == 0), function priv_generic_find, file segment_manager.hpp, line 877.  
Abort trap: 6  
```  
At least in my repro, this is due to "simple user error" in my client code: I'm putting a `shm::vector` into the shared memory object but attempting to read it out as if it were a `std::vector`. This obviously cannot _work._ However, I was very surprised to see that it crashed the entire process with an assert-fail! I expected it either to detect the mismatch and throw an exception, or fail-to-detect the mismatch and just do some wacky UB. Is it really intended that it should detect the problem and yet _not_ throw something recoverable?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-08-22 22:38:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/228#issuecomment-2305864748  

Well, it's absolutely UB, so anything can happen. It's like passing a void pointer to a thread and casting it to a different type. I don't think a user can do anything to recover from such error if an exception was thrown.  
  
Or if you use a different ABI between the server and the client (like one 32 bit process and a 64 bit process reader).  
  
In release mode you won't have an assertion, but UB.

---

## Comment 2

> Username: Quuxplusone  
> Created at: 2024-08-23 03:01:24 UTC  
> Updated at: 2024-08-23 03:02:03 UTC  
> Url: https://github.com/boostorg/interprocess/issues/228#issuecomment-2306093042  

> Well, it's absolutely UB, so anything can happen. [...] In release mode you won't have an assertion, but UB.  
  
Okay, so whatever the failure mode is, it's not something that Boost can 100% reliably detect; is that right? If so, then I'm happy to consider this closed. (And #88 _might_ be the same thing, i.e., user error; but I don't _know_ that it is.)

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-09-26 11:59:49 UTC  
> Url: https://github.com/boostorg/interprocess/issues/228#issuecomment-2376739231  

Sorry for not replying earlier. Right, Boost can't detect this issue, so I will close the issue. And thanks for using Boost.Interprocess for your allocator_aware inplace_vector paper. Maybe I should implement it for boost::container::static_vector...

---

## Comment 4

> Username: Quuxplusone  
> Created at: 2024-09-26 14:29:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/228#issuecomment-2377141133  

> Sorry for not replying earlier. Right, Boost can't detect this issue, so I will close the issue. And thanks for using Boost.Interprocess for your allocator_aware inplace_vector paper. Maybe I should implement it for boost::container::static_vector...  
  
I'd support that. :) Although `boost::container::static_vector` is _already_ a template of three parameters — its third parameter is `class Options`. Which probably makes such a change extremely non-trivial.  
  
(And, although this is unrelated, I'd love to see static_vector gain support for memmove'ing trivially relocatable types! That's something else all the SG14 containers do, which Boost currently doesn't, and which again I'm quite willing to help with.  
https://github.com/Quuxplusone/SG14/blob/master/include/sg14/inplace_vector.h#L589-L604  
https://godbolt.org/z/6hP99jz3T  
)
