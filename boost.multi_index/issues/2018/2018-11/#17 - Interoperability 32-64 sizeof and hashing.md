# #17 - Interoperability 32/64 sizeof and hashing [Closed]

> Username: CerosDev  
> Created at: 2018-11-12 16:15:41 UTC  
> Updated at: 2019-01-07 16:48:43 UTC  
> Closed at: 2018-11-12 17:16:31 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17  

**Situation**  
We are storing a Multi-Index in a Boost Interprocess Shared Memory that is accessed by a 32bit process and a 64bit process. The 32bit process or the 64bit process can be the creator of the object.  
  
**Symptoms**  
If a 64bit process creates the Multi-Index, the 32bit process will crash trying to retrieve it and vice-versa.  
If a 64bit process inserts object into te Multi-Index, the 32bit process will fail to retrieve them by hashing and vice-versa.  
  
**Solution**  
I had to modify some headers so that the sizeof of the Multi-Index is exactly the same in 32 and 64bits.   
In my own code, I force the hashing to be on 32bit instead of `size_t`. I extracted some Boost hash function to do so. But there is still issue with the `composite_key`. It will perform bitwise operation on a `size_t carry` which will yield different result if on a 32bit or 64bit.  
  
See patch attached.  
[boost-1.68.0-fix-interoperability-32-64-multi_index.patch.zip](https://github.com/boostorg/multi_index/files/2572624/boost-1.68.0-fix-interoperability-32-64-multi_index.patch.zip)  
  
See related issue for Boost Unordered https://github.com/boostorg/unordered/issues/9

---

## Comment 1

> Username: joaquintides  
> Created at: 2018-11-12 17:16:31 UTC  
> Updated at: 2018-11-12 17:28:27 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-437960600  

Hi,  
  
Thanks for the report and congrats on the ingenuity you've used to make this unexpected scenario work. I have to say, though, that 32/64 interprocess compatibility is not (by far) one feature I'm willing to support to the point of fixing the size of internal size-storing variables to either 32 or 64 bits (by the way, your patch seems inconsistent in that `std::size_t`s are converted in some places to `uint64_t` (like `size_` and `capacity_` in `rnd_index_ptr_array.hpp`) and to `uint32_t` in some other (`node_count` in `multi_index_container.hpp`)).  
  
I'm sorry to say you'll have to stick to patching your local copy of Boost to cope with the very exotic scenario you're dealing with.  
  
Best regards,

---

## Comment 2

> Username: CerosDev  
> Created at: 2018-11-12 18:05:11 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-437975943  

What about leaving the flexibility to the user via a template parameter? By default it could remain std::size_t and use the SFINAEd policy. Would that be feasible?

---

## Comment 3

> Username: joaquintides  
> Created at: 2018-11-12 18:29:20 UTC  
> Updated at: 2018-11-12 18:43:14 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-437983233  

Can you do a small test for me? Please check what the types `difference_type` and `size_type` alias to in the Boost.Interprocess allocators you're using, both in 32 and 64 bit mode. If they resolve to the same fixed-size types in both modes, something could potentially be done.

---

## Comment 4

> Username: CerosDev  
> Created at: 2018-11-12 18:44:43 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-437987936  

template<typename SharedType>  
    using OffsetPtr = boost::interprocess::offset_ptr<SharedType, int32_t, uint64_t, sizeof(std::uint64_t)>;  
      
    using ManagedWindowsSharedMemory =  
    boost::interprocess::basic_managed_windows_shared_memory<char,  
      boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family, OffsetPtr<void>, sizeof(std::uint64_t)>,  
      boost::interprocess::iset_index  
    >;  
      
    typedef ManagedWindowsSharedMemory::segment_manager ShMemSegmentManager;  
    typedef boost::interprocess::allocator<int, ShMemSegmentManager> IndexedDataAllocator;  
      
    int main(int argc, char **argv)  
    {  
      std::cout << "size_type: " << typeid(IndexedDataAllocator::size_type).name() << std::endl;  
      std::cout << "difference_type: " << typeid(IndexedDataAllocator::difference_type).name() << std::endl;  
      return 0;  
    }  
  
  
x86 Output:  
  
    size_type: unsigned int  
    difference_type: int  
  
x64 Output:  
  
    size_type: unsigned int  
    difference_type: int

---

## Comment 5

> Username: joaquintides  
> Created at: 2018-11-21 19:53:23 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-440790170  

OK, so the types are _nominally_ the same in 32 and 64 modes :-(  
  
This is what I can do:  
  
* Currently `difference_type` and `size_type` are hard-wired to `std::ptrdiff_t` and `std::size_type`, respectively. I can make these inherit from the corresponding types in the allocator, which is perfectly reasonable and general (and in fact probably more correct than the current situation).  
* The interprocess allocators you use in 32 and 64 modes have to modified (probably by inheriting from the Boost-provided ones and redefining types) so that their `difference_type` and `size_type` have the same storage in both modes (for instance, defining them to `int32_t` and `unit32_t`, respectively).  
 * As for the hashing carry size, I can't provide you with any solution from my side. You'd have to keep patching your Boost copy or (preferrably) writing your own version of `composite_key_hash` for private use.  
  
If this strategy is acceptable to you, please let me know and I'll try to find some time in the future to make my part.

---

## Comment 6

> Username: CerosDev  
> Created at: 2018-11-22 14:50:02 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-441052564  

That is perfectly acceptable. We will create our own `composite_key_hash` for this.   
  
But what about the unordered containers (https://github.com/boostorg/unordered/issues/9). In `include/boost/unordered/detail/implementation.hpp` around the `pick_policy` it will use a different algo in 64 and also perform bitwise operation on a `size_t` which will yield a different result...  
  
Thank you very much!

---

## Comment 7

> Username: joaquintides  
> Created at: 2018-11-23 08:52:11 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-441183027  

> But what about the unordered containers (boostorg/unordered#9) [...]  
  
Sorry, I don't see what Boost.Unordered has to do with our discussion. Boost MultiIndex and Boost.Unordered are completely independent libraries.

---

## Comment 8

> Username: CerosDev  
> Created at: 2018-11-26 16:19:59 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-441700364  

Sorry my bad. I will update my other issue. Thanks for your support.

---

## Comment 9

> Username: joaquintides  
> Created at: 2018-12-30 11:20:37 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-450554048  

I've just made the changes necessary to use the allocator's `size_type` and `difference_type`. You can download the results at  
  
https://github.com/boostorg/multi_index/archive/9acee043bc093cd50f95582bf9172370dc6a7a7e.zip  
  
I'd very much appreciate if you can test locally and report back if this solves your issue. Also, please report any new warning you might encounter so that I can fix it in the codebase.

---

## Comment 10

> Username: CerosDev  
> Created at: 2019-01-07 16:48:43 UTC  
> Url: https://github.com/boostorg/multi_index/issues/17#issuecomment-452000219  

Thanks for this fix. Everything seems to be working correctly in a 32/64 bits combinaison with a Boost shared memory.   
I did not detect any new warning.
