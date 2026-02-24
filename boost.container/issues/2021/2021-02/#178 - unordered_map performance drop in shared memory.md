# #178 - unordered_map performance drop in shared memory [Closed]

> Username: liuzqt  
> Created at: 2021-02-02 23:56:58 UTC  
> Updated at: 2021-02-03 10:32:59 UTC  
> Closed at: 2021-02-03 10:32:59 UTC  
> Url: https://github.com/boostorg/container/issues/178  

I would like to boost shared memory in my project, where I would have several `unordered_map`.   
  
I was trying to do some simple benchmark to understand the performance of `unordered_map` in shared memory(more specifically, my usecase is read-only, i.e., construct the unordered_map at the beginning, with ~1 million data,  and perform many queries on that, thus I only care about the read performance).  
  
Basically, I expect no performance downgrade in my read-only usecase, except the shared memory initialization, since shared memory should have no difference once mapped to process virtual address space.  
  
I've done some basic benchmark on:  
  
- stl unordered_map  
- boost unordered_map  
- stl unordered_map in shared_memory  
- boost unordered_map in shared_memory  
  
below is the benchmark result I got  
```  
bench stl map  
query size 1000000 time: 43ms  
  
bench boost map  
query size 1000000 time: 47ms  
  
bench boost map in shared mem  
query size 1000000 time: 70ms  
  
bench stl map in shared mem  
query size 1000000 time: 44ms  
```  
the benchmark code can be found at https://github.com/liuzqt/boost_shared_memory_experiment  
  
all codes are compiled with -O3 optimization  
  
my compiler version is `g++ (Ubuntu 5.4.0-6ubuntu1~16.04.12) 5.4.0 20160609`  
  
I know this might not be a very good benchmark, but I do see significant downgrade with boost unordered_map in shared_memory.  
  
One guess I have about this performance drop is that within boost shared memory, pointer type become offset_ptr, and always have to deal with extra jump/offset operation. And in batch unordered_map query, if in opening hash implementation, the main runtime might focus on address jumping in linked list, which have extra overhead in shared memory due to offset_ptr.  
  
I just want to confirm my guess, or if I have done something wrong? And if that's the case, is there anything I can tune to improve `boost::unordered_map` performance in shared memory?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-03 10:32:59 UTC  
> Url: https://github.com/boostorg/container/issues/178#issuecomment-772405856  

Unordered map is not a container from Boost.Container, but Boost.Unordered. In any case, your analysis is correct, address jumping code suffers more with offset_ptr as there are more calculations and the compiler can't also guess things it might guess with raw pointers. I don't think you can do anything to tune boost::unordered_map, as the implementation is generic. It's just that shared memory implies an overhead that you have to analyse it's affordable or not for your application. Thank!
