# #136 - unordered_map performance in shared memory [Closed]

> Username: liuzqt  
> Created at: 2021-01-28 00:36:46 UTC  
> Updated at: 2021-02-03 10:29:16 UTC  
> Closed at: 2021-02-03 10:29:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/136  

I would like to introduce shared memory in my project, where I would have several unordered_map. I was trying to do some simple benchmark to understand the performance in shared memory(more specifically, my usecase is read-only, i.e., construct the unordered_map at the beginning, and perform many queries on that, thus I only care about the read performance).  
  
Basically, I expect **no performance downgrade** in my read-only usecase, except the shared memory initialization, since shared memory should have no difference onced mapped to process virtual address space.  
  
I've done some basic benchmark on:  
  
- stl unordered_map  
- boost unordered_map  
- stl unordered_map in shared_memory  
- boost unordered_map  in shared_memory  
  
  
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
  
all codes are compiled with `-O3` optimization  
  
I know this might not be a very good benchmark, but I do see significant downgrade with **boost unordered_map** in shared_memory, which is beyond my expectation.  
  
the benchmark code can be found at https://github.com/liuzqt/boost_shared_memory_experiment  
  
Am I doing something wrong with shared memory, or the downgrade is expected result with shared memory implementation itself?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-01-30 23:35:47 UTC  
> Url: https://github.com/boostorg/interprocess/issues/136#issuecomment-770297388  

Performance in shared memory can't be the same because instead of raw pointers, containers need to use relative pointers. In any case, the performance hit should be a bug in Boost.Unordered instead of Boost.Interprocess. You didnt' provide any hint about the compiler version, etc. are you sure the std map is not using raw pointers?

---

## Comment 2

> Username: liuzqt  
> Created at: 2021-02-02 23:48:38 UTC  
> Url: https://github.com/boostorg/interprocess/issues/136#issuecomment-772094644  

Thanks for the answer!  
  
my compiler version is `g++ (Ubuntu 5.4.0-6ubuntu1~16.04.12) 5.4.0 20160609`  
  
Given that containers in shared memory are using relative pointers, then performance hit is understandable, especially for unordered_map, I suppose they're using opening hashing implementation, and batch query must involve several pointer/address jump, which leads to the perforamnce loss.  
  
And BTW, I realized that `std::unordered_map` is actually not compatible with boost shared memory, and my benchmark result is wrong (the code about **std::unordered_map + boost shared memory** can compile and run with single process, but trying to run multiprocess will lead to crash, so I believe `std::unordered_map` is using raw pointer internally, it's just happened to be ok with shared memory in single process)

---

## Comment 3

> Username: igaztanaga  
> Created at: 2021-02-03 10:29:16 UTC  
> Url: https://github.com/boostorg/interprocess/issues/136#issuecomment-772403633  

Thanks for the explanations. Yes, it was suspicious that std performance was equal for shared memory or heap memory. You are right that heavily pointer based data structures suffer more with offset pointers. After your explanations, I'm closing the bug.
