# #75 - 32bits shd mem + Unordered crash in rb_tree when clearing in /O2 [Closed]

> Username: CerosDev  
> Created at: 2018-12-18 16:14:33 UTC  
> Updated at: 2024-10-03 08:58:04 UTC  
> Closed at: 2024-10-03 08:58:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/75  

I've set up a Boost IPC shared memory for 32/64bits communication as per comment from Boost header so `offset_ptr<SharedType, int32_t, uint64_t, sizeof(std::uint64_t)>` along with rbtree_best_fit's MemoryAlignment `sizeof(std::uint64_t)`  
  
Boost 1.68  
  
In x86 VS 15.8.8 with `/O2 + /Ob2 + /Oi + /Ot + /Oy` and `BOOST_INTERPROCESS_DISABLE_FORCEINLINE`  
  
The rbtree_algorithms will crash trying to rebalance after erasure. But if I disable the optimisation `/Od`, everything will be fine.  
  
This is my example program:  
  
    template<typename SharedType>  
    using OffsetPtr = boost::interprocess::offset_ptr<SharedType, int32_t, uint64_t, sizeof(std::uint64_t)>;  
  
    using ManagedWindowsSharedMemory =  
    boost::interprocess::basic_managed_windows_shared_memory<char,  
      boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, OffsetPtr<void>, sizeof(std::uint64_t)>,  
      boost::interprocess::iset_index  
    >;  
  
    using ShMemSegmentManager   = ManagedWindowsSharedMemory::segment_manager;  
    using shmMultiMapAllocator  = boost::interprocess::allocator<std::pair<unsigned int const, unsigned int>, ShMemSegmentManager>;  
    using shmMultiMap           = boost::unordered_multimap<unsigned int, unsigned int, boost::hash<unsigned int>, std::equal_to<unsigned int>, shmMultiMapAllocator>;  
  
  
    // Win32: /O2 + /Ob2 + /Oi + /Ot + /Oy + BOOST_INTERPROCESS_DISABLE_FORCEINLINE  
    //        crash at rbtree_algorithms::rebalance_after_erasure_restore_invariants  
    //        const node_ptr x_parent_left(NodeTraits::get_left(x_parent));  
    //        0xC0000005: Access violation reading location 0x00000008.  
  
    int main(int argc, char **argv)  
    {  
      auto managedShMem = new ManagedWindowsSharedMemory(boost::interprocess::create_only, "shmName", 2*1024*1024, 0);  
  
      auto mSpecimenToFileMap = managedShMem->find_or_construct<shmMultiMap>("specimenToFileMap")  
        (3, boost::hash<unsigned int>(), std::equal_to<unsigned int>(), shmMultiMapAllocator(managedShMem->get_segment_manager()));  
        
      for (int i = 0; i < 5000; ++i)  
      {  
        mSpecimenToFileMap->insert(std::make_pair<unsigned int, unsigned int>(std::move(unsigned int(i/10)), std::move(unsigned int(i))));  
      }  
      mSpecimenToFileMap->clear();  
  
  
      system("pause");  
      return 0;  
    }  
  
  
In x64 without `BOOST_INTERPROCESS_DISABLE_FORCEINLINE`, I do not have any issue.  
  
In x86, if I use `simple_seq_fit` instead of `rbtree_best_fit`, it will not crash with the optimisation on.

---

## Comment 1

> Username: CerosDev  
> Created at: 2018-12-18 20:20:21 UTC  
> Updated at: 2018-12-18 20:21:40 UTC  
> Url: https://github.com/boostorg/interprocess/issues/75#issuecomment-448356583  

I'm using Vs 2017 so in the Optimization I selected `custom`.  
[According to Microsoft](https://docs.microsoft.com/en-us/cpp/build/reference/o1-o2-minimize-size-maximize-speed?view=vs-2017), `/O2` is equivalent to `/Og /Oi /Ot /Oy /Ob2 /GF /Gy`  
  
By toggling them, I managed to find the culprit: [`/Og`](https://docs.microsoft.com/en-us/cpp/build/reference/og-global-optimizations?view=vs-2017)  
  
So in Win32:  
`/Og /Oi /Ot /Oy /Ob2 /GF /Gy` will crash  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`/Oi /Ot /Oy /Ob2 /GF /Gy` will NOT crash

---

## Comment 2

> Username: 363568233  
> Created at: 2018-12-19 01:22:54 UTC  
> Updated at: 2018-12-19 01:24:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/75#issuecomment-448434548  

I have a similar crash problem in rbtree_best_fit Boost 1_65_1  x64  with large size mapped_region, when clear or erase.  
I don't know if it's a compiling optimization problem.  
I use unordered_map in shared memory,  but when I replace rbtree_best_fit with simple_seq_fit, a lot of compiling error.  
Could you tell me how to replace rbtree_best_fit with simple_seq_fit without compiling error??

---

## Comment 3

> Username: igaztanaga  
> Created at: 2024-10-03 08:58:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/75#issuecomment-2390884734  

Closing stale bugs. Please re-submit if recent Interprocess versions show the same error.
