# #215 - Alignment problem with boost/interprocess/segment_manager on SPARC 32Bit / Boost 1.84 [Closed]

> Username: TomSmartBishop  
> Created at: 2024-03-14 11:31:23 UTC  
> Updated at: 2024-09-27 08:43:21 UTC  
> Closed at: 2024-09-26 21:12:05 UTC  
> Url: https://github.com/boostorg/interprocess/issues/215  

As requested by @igaztanaga on https://cpplang.slack.com/archives/C27KZLB0X/p1710326590253599 I'm opening an issue here.  
  
I'm creating a shared memory:  
```  
TEST(SharedMemoryManagerTest, PlainTest) {  
	struct TestData {  
		std::chrono::time_point<std::chrono::steady_clock>  c1{};  
		std::chrono::time_point<std::chrono::steady_clock>  c2{};  
		uint32_t data;  
	};  
	auto unnamedRegion = std::make_unique<boost::interprocess::mapped_region>(  
			boost::interprocess::anonymous_shared_memory(512*1024));  
	auto anonymousShm = std::make_unique<boost::interprocess::managed_external_buffer>(  
			boost::interprocess::create_only, unnamedRegion->get_address(), unnamedRegion->get_size());  
  
	auto result = anonymousShm->find_or_construct<TestData>("test"); //kaboom  
	ASSERT_NE(nullptr, result.operator()());  
}  
```  
  
That is the output:  
```  
[----------] 1 test from SharedMemoryManagerTest  
[ RUN      ] SharedMemoryManagerTest.PlainTest  
Start  foo: SharedMemoryManagerTest.PlainTest  
Test  #foo: SharedMemoryManagerTest.PlainTest  
.............................................................................Bus error***Exception:   1.28 sec  
```  
Tested on other platforms x86, 32&64Bit (Win/Linux) without any issues.  
  
I thought it might be due to segment_manager.hpp priv_generic_named_construct(...) there is an allocate performed and not allocate_aligned despite having the info in block_info.m_value_alignment available.

---

## Comment 1

> Username: TomSmartBishop  
> Created at: 2024-03-26 14:49:34 UTC  
> Updated at: 2024-03-26 14:50:47 UTC  
> Url: https://github.com/boostorg/interprocess/issues/215#issuecomment-2020907428  

I changed the test a bit that clearly shows that the allocated memory is 4 byte aligned(address 0xfe93005c) even though int64 would need 8 byte alignment:  
  
```  
TEST(SharedMemoryManagerTest, TestInt64) {  
	struct Int64Data {  
		uint64_t data;  
		Int64Data() {};  
	};  
	auto unnamedRegion =  
			std::make_unique<patchboost::interprocess::mapped_region>(patchboost::interprocess::anonymous_shared_memory(512 * 1024));  
	auto anonymousShm = std::make_unique<patchboost::interprocess::managed_external_buffer>(  
			patchboost::interprocess::create_only, unnamedRegion->get_address(), unnamedRegion->get_size());  
	std::cout << "Find or construct int 64 data... Alignment " << alignof(Int64Data) << " (size " << sizeof(Int64Data) << ")" <<  std::endl;  
	auto* int64data = anonymousShm->find_or_construct<Int64Data>("int64_data")();  
	std::cout << "Access int64 data..." << int64data <<  
		((reinterpret_cast<std::uintptr_t>(int64data)%alignof(Int64Data)==0) ? " Aligned" : " NOT Aligned") << std::endl;  
	if(int64data) {  
		std::cout << "Write int64 data..." << std::endl;  
		int64data->data = 64;  
	}  
	std::cout << "Done." << std::endl; //never reached due to bu error when writing to int64  
}  
```  
  
```  
[----------] 1 test from SharedMemoryManagerTest  
[ RUN      ] TestInt64  
Find or construct int 64 data... Alignment 8 (size 8)  
Access int64 data...0xfe93005c NOT Aligned  
Write int64 data...  
................................................Bus error***Exception:   1.26 sec  
```

---

## Comment 2

> Username: TomSmartBishop  
> Created at: 2024-03-27 14:24:11 UTC  
> Updated at: 2024-03-27 14:24:29 UTC  
> Url: https://github.com/boostorg/interprocess/issues/215#issuecomment-2022901951  

I investigated that a bit further and found that in **priv_generic_named_construct** the **intrusive_hdr** is still created on an aligned address, my example 0xfe930048.  
When calling **intrusive_hdr->get_block_header()** for creating the **block_header_t**, I however get the the address 0xfe930054 (maybe because size_type has only 4 bytes?). From there it adds another 8 bytes and I land on 0xfe93005c (just like in the test).

---

## Comment 3

> Username: TomSmartBishop  
> Created at: 2024-03-27 15:12:43 UTC  
> Url: https://github.com/boostorg/interprocess/issues/215#issuecomment-2023024713  

@igaztanaga I did one more experiment and just changed intrusive_value_type_impl::BlockHdrAlignment in segment_manager_helper.hpp from   
`enum  {  BlockHdrAlignment = ::patchboost::container::dtl::alignment_of<block_header<size_type> >::value  };`  
to  
`enum  {  BlockHdrAlignment = 8};`  
(ignoring any possible side effects/implications) and it worked. The int64 is indeed aligned. I guess, since the header comes before the data, we have to make sure, that the alignment fits after the header, right?

---

## Comment 4

> Username: pgroke-dt  
> Created at: 2024-04-04 17:09:09 UTC  
> Url: https://github.com/boostorg/interprocess/issues/215#issuecomment-2037748326  

I want to add that this is not only an issue on SPARC 32 bit, it's an issue on other 32 bit platforms as well. Only on most other platforms you don't get the nice bus error that tells you something's wrong. E.g. I ran the test on x86 32 bit, Visual Studio 2022, and I also get bad alignment there. The test doesn't crash, but the address for the `uint64_t` is not is not divisible by 8.  
  
And this can become a problem when using atomics. On x86 (and I assume most/all other CPUs), normal memory accesses are only guaranteed to be atomic if the memory location is properly aligned. x86 won't give you a bus error. But if the access happens to span a cache line boundary, it will simply not be atomic. AFAIK this is not a problem for `lock cmpxchg`, `lock cmpxchg8b` etc. - those are guaranteed to always be atomic wrt. other interlocked instructions. But it _is_ a problem for normal loads and stores - which are typically used for atomic "load acquire" and "store release" operations on x86.  
  
----  
  
I'm assuming that it's quite popular to use atomic variables in shared memory objects. And if this bug breaks something, it will be extremely hard to track down the root cause - at least on platforms that silently allow unaligned accesses. As such, I consider this a serious issue.

---

## Comment 5

> Username: pgroke-dt  
> Created at: 2024-04-04 17:37:18 UTC  
> Url: https://github.com/boostorg/interprocess/issues/215#issuecomment-2037804068  

Wrt. what goes wrong and how it could be fixed...  
  
The objects that are constructed into the allocated memory regions are (in layout order):  
* The `intrusive_value_type_impl` header  
* The `block_header`  
* The actual `T`  
  
Again with x86, 32 bit, Visual Studio 2022:  
The memory allocation itself works properly, the returned address is aligned to "max align". `intrusive_value_type_impl` has 12 bytes. `block_header` has 8 bytes and an alignment requirement of 4. `T` has 8 bytes and an alignment requirement of 8.  
  
The offset for `intrusive_value_type_impl` is trivially correct because it's zero.  
  
The offset for `block_header` is computed by rounding 12 (size of `intrusive_value_type_impl`) up to the next multiple of 4 (align of `block_header`). The result is 12. In itself, this is OK. `block_header` needs an alignment of 4, so 12 is fine.  
  
Then the offset for the `T` is computed. The problem is that this offset computation is done relative to the beginning of the `block_header` - and doesn't take into account that the `block_header` itself might not be suitable aligned for arbitrary types. I.e. the offset of the `T` relative to the address of the `block_header` is computed by rounding 8 (size of `block_header`) up to the next multiple of 8 (align of `T`), which gives 8.  
  
So the offset for `T` from the beginning of the allocation is 12 + 8 = 20. Which is not divisible by 8. Kaboom.  
  
----  
  
I see two ways to fix this.  
  
The simple one would be to use "max align" for `BlockHdrAlignment`. This requires few code changes, but it wastes some memory in some cases. Like if we take the example above, only with a 4 byte `T`, then placing the `block_header` at offset 12 and the `T` at offset 20 would be fine. But using "max align" for `BlockHdrAlignment` would insert 4 bytes of unnecessary padding.  
  
The more complicated one would be to change the offset calculation for the `T`, so that it accounts for the actual offset of the `block_header` from the beginning of the allocation. This requires a lot more changes, will be harder to get correct and will also make the code even harder to read. (It might also add a very small runtime overhead, but I don't expect that to matter much.)

---

## Comment 6

> Username: igaztanaga  
> Created at: 2024-09-26 21:12:05 UTC  
> Url: https://github.com/boostorg/interprocess/issues/215#issuecomment-2377947104  

Many thanks for all your reports and diagnosis. As you've discovered the implementation does not properly align objects in some cases. A new implementation (and thus ABI changed) was needed to fix this. This was implemented starting in commit https://github.com/boostorg/interprocess/commit/cbeb5b5ea9d363484306b33c0a0ad304914ed3f3 with some optimizations in later commits.   
  
Tests have been expanded to include overaligned types and checking the alignment of returned addresses. Many thanks again for the report.

---

## Comment 7

> Username: pgroke-dt  
> Created at: 2024-09-27 08:43:20 UTC  
> Url: https://github.com/boostorg/interprocess/issues/215#issuecomment-2378765828  

@igaztanaga thanks so much for fixing this! Now we can remove our local patch after updating to the next Boost version. Nice :)  
(We implemented the much easier version that wastes space and doesn't support over-aligned types. I was pretty sure that you'd want a better implementation, which is why I didn't try to submit the patch.)
