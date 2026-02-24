# #192 - managed_windows_shared_memory crash on destruction [Closed]

> Username: projMiss  
> Created at: 2023-02-01 04:51:51 UTC  
> Updated at: 2024-08-08 01:01:10 UTC  
> Closed at: 2024-08-07 20:46:18 UTC  
> Url: https://github.com/boostorg/interprocess/issues/192  

After defining BOOST_INTERPROCESS_FORCE_NATIVE_EMULATION, when there are more than 5 synchronization objects,  
managed_windows_shared_memory will crash on destruction.  
### code  
```  
#include <iostream>  
  
#ifndef BOOST_INTERPROCESS_FORCE_NATIVE_EMULATION  
#define BOOST_INTERPROCESS_FORCE_NATIVE_EMULATION  
#endif  
  
#include <boost/interprocess/managed_windows_shared_memory.hpp>  
#include <boost/interprocess/allocators/allocator.hpp>  
#include <boost/interprocess/containers/vector.hpp>  
#include <boost/interprocess/smart_ptr/unique_ptr.hpp>  
#include <boost/interprocess/sync/interprocess_mutex.hpp>  
#include <boost/interprocess/sync/interprocess_condition.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
  
namespace bip = boost::interprocess;  
  
struct SharedData  
{  
	// commenting out one of the synchronization objects is fine  
	bip::interprocess_mutex initMutex;  
	bip::interprocess_condition initCond;  
  
	bip::interprocess_mutex statusMutex;  
	bip::interprocess_condition statusCond;  
  
	bip::interprocess_mutex inputMutex;  
	bip::interprocess_condition inputCond;  
};  
  
void test()  
{  
	bip::managed_windows_shared_memory sharedMemory(bip::create_only, "TestSharedMemory", 32 * 1024 * 1024);  
	SharedData* sharedData = sharedMemory.construct<SharedData>("TestSharedData")();  
	if (sharedData == nullptr)  
	{  
		std::cout << "construct failed!" << std::endl;  
		return;  
	}  
  
	sharedMemory.destroy_ptr(sharedData);  
}  
  
int main()  
{  
	test();  
    std::cout << "Hello World!" << std::endl;  
	system("pause");  
	return 0;  
}  
```  
### output  
```  
Exception thrown: read access violation.  
boost::unordered::detail::grouped_bucket_iterator<boost::unordered::detail::bucket<boost::unordered::detail::node<std::pair<boost::interprocess::ipcdetail::sync_id const ,void * __ptr64>,void * __ptr64>,void * __ptr64> >::operator++(...).**p** was 0xFFFFFFFFFFFFFFFF.  
```  
### call stack  
```  
Project1.exe!boost::unordered::detail::iterator_detail::c_iterator<boost::unordered::detail::node<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>,void *>,boost::unordered::detail::bucket<boost::unordered::detail::node<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>,void *>,void *>>::increment() Line 1864	C++  
Project1.exe!boost::unordered::detail::iterator_detail::c_iterator<boost::unordered::detail::node<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>,void *>,boost::unordered::detail::bucket<boost::unordered::detail::node<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>,void *>,void *>>::operator++() Line 1802	C++  
Project1.exe!boost::unordered::detail::table<boost::unordered::detail::map<std::allocator<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>>,boost::interprocess::ipcdetail::sync_id,void *,boost::hash<boost::interprocess::ipcdetail::sync_id>,std::equal_to<boost::interprocess::ipcdetail::sync_id>>>::erase_node(boost::unordered::detail::iterator_detail::c_iterator<boost::unordered::detail::node<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>,void *>,boost::unordered::detail::bucket<boost::unordered::detail::node<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>,void *>,void *>> pos) Line 2923	C++  
Project1.exe!boost::unordered::unordered_map<boost::interprocess::ipcdetail::sync_id,void *,boost::hash<boost::interprocess::ipcdetail::sync_id>,std::equal_to<boost::interprocess::ipcdetail::sync_id>,std::allocator<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>>>::erase(boost::unordered::detail::iterator_detail::iterator<boost::unordered::detail::node<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>,void *>,boost::unordered::detail::bucket<boost::unordered::detail::node<std::pair<boost::interprocess::ipcdetail::sync_id const ,void *>,void *>,void *>> position) Line 1932	C++  
Project1.exe!boost::interprocess::ipcdetail::sync_handles::destroy_syncs_in_range(const void * addr, unsigned __int64 size) Line 253	C++  
Project1.exe!boost::interprocess::mapped_region::destroy_syncs_in_range<0>(const void * addr, unsigned __int64 size) Line 912	C++  
Project1.exe!boost::interprocess::mapped_region::priv_close() Line 561	C++  
Project1.exe!boost::interprocess::mapped_region::~mapped_region() Line 275	C++  
Project1.exe!boost::interprocess::ipcdetail::managed_open_or_create_impl<boost::interprocess::windows_shared_memory,8,0,0>::~managed_open_or_create_impl<boost::interprocess::windows_shared_memory,8,0,0>() Line 219	C++  
Project1.exe!boost::interprocess::basic_managed_windows_shared_memory<char,boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family,boost::interprocess::offset_ptr<void,__int64,unsigned __int64,0>,0>,boost::interprocess::iset_index>::~basic_managed_windows_shared_memory<char,boost::interprocess::rbtree_best_fit<boost::interprocess::mutex_family,boost::interprocess::offset_ptr<void,__int64,unsigned __int64,0>,0>,boost::interprocess::iset_index>() Line 230	C++  
Project1.exe!test() Line 41	C++  
Project1.exe!main() Line 46	C++  
Project1.exe!invoke_main() Line 79	C++  
Project1.exe!__scrt_common_main_seh() Line 288	C++  
Project1.exe!__scrt_common_main() Line 331	C++  
Project1.exe!mainCRTStartup(void * __formal) Line 17	C++  
kernel32.dll!00007ff9c6f37614()	Unknown  
ntdll.dll!00007ff9c85226a1()	Unknown  
```  
### environment  
win10  
vs2022  
boost_1_81_0: b2 stage link=static runtime-link=shared threading=multi define=BOOST_USE_WINAPI_VERSION=0x0A00 --without-python

---

## Comment 1

> Username: projMiss  
> Created at: 2024-08-08 01:01:08 UTC  
> Url: https://github.com/boostorg/interprocess/issues/192#issuecomment-2274672102  

Thank you!
