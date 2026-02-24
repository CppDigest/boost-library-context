# #123 - Deadlock on Windows for interprocess_condition stored in shared memory [Closed]

> Username: bhumbers  
> Created at: 2020-07-31 14:43:25 UTC  
> Updated at: 2021-02-11 12:00:50 UTC  
> Closed at: 2021-02-11 11:59:53 UTC  
> Url: https://github.com/boostorg/interprocess/issues/123  

We ran into an interesting deadlock situation with `interprocess_condition` and `managed_windows_shared_memory` on Windows. The first time our process runs as expected, but the second time it runs there's deadlock on a call to `interprocess_condition::timed_wait()`.  
  
There's a proposed fix that we'll push shortly, but I wanted to document the issue for reference.  
  
# Minimal repro  
  
Build & run `Main1`, leave it running in the background  
```  
// Main 1: Open a clean shared memory segment and block forever  
  
#ifdef _WIN32  
#include <boost/interprocess/detail/workaround.hpp>  
#undef BOOST_INTERPROCESS_FORCE_GENERIC_EMULATION  
#include <boost/interprocess/managed_windows_shared_memory.hpp>  
#endif  
  
#include <boost/interprocess/shared_memory_object.hpp>  
  
#include <iostream>  
  
using namespace boost::interprocess;  
  
int main(int argc, char* argv[]) {  
  // Create a a new shared memory segment "Test" that will be used by both Main 1 and Main 2  
  shared_memory_object::remove("Test");  
  managed_windows_shared_memory managed_shm(open_or_create, "Test", 1024);  
  std::cout << "Created shared memory segment \"Test\"" << std::endl;  
  std::cout << "Blocking forever to keep shared memory reference alive..." << std::endl;  
  while (true) {  
    continue;  
  }  
}  
```  
  
Build & run `Main2` *twice*  
```	  
// Main 2: Obtain interprocess_condition and do a timed_wait for 1 ms  
// Expected behavior: timed_wait returns and program exits  
// Actual behavior: On 2nd run while Main1 is running, timed_wait never returns  
  
#ifdef _WIN32  
#include <boost/interprocess/detail/workaround.hpp>  
#undef BOOST_INTERPROCESS_FORCE_GENERIC_EMULATION  
#include <boost/interprocess/managed_windows_shared_memory.hpp>  
#endif  
  
#include <boost/interprocess/sync/interprocess_condition.hpp>  
#include <boost/interprocess/sync/interprocess_mutex.hpp>  
#include <boost/interprocess/sync/scoped_lock.hpp>  
#include <boost/thread/thread_time.hpp>  
#include <iostream>  
  
using namespace boost::interprocess;  
  
int main(int argc, char* argv[]) {  
  managed_windows_shared_memory managed_shm(open_only, "Test");  
  auto mutex_ipc = managed_shm.find_or_construct<interprocess_mutex>("mutex_ipc")();  
  
  auto condition_ipc = managed_shm.find_or_construct<interprocess_condition>("condition_ipc")();  
  
  scoped_lock<interprocess_mutex> lock(*mutex_ipc);  
  std::cout << "About to start timed_wait for 1 ms..." << std::endl;  
  condition_ipc->timed_wait(lock, boost::get_system_time() + boost::posix_time::milliseconds(1));  
  std::cout << "Exiting after timed_wait ended" << std::endl;  
}  
```  
  
**Expected Result**: `Main2` prints `Exiting after timed_wait ended` and exits after 2nd run  
**Actual Result**: `Main2` deadlocks in its 2nd run during the call to `condition_ipc->timed_wait(...)`.   
   
# Suspected Cause  
  
A few details to note  
* The `interprocess_condition` variable is stored in IPC memory.  
* `#undef BOOST_INTERPROCESS_FORCE_GENERIC_EMULATION` is required so that the Windows-specific implementation is used (generic implementation doesn't show this issue).  
* `Main1` must be running in the background throughout the test in order to repro the issue. Otherwise, the shared memory block "Test" may be automatically cleaned up when `Main2` exits, which breaks the repro.  
  
The problem lies with the initial count that `boost::interprocess::ipcdetail::windows_semaphore` uses when obtaining a  Windows semaphore handle on the 1st run (nothing in IPC memory) vs the 2nd run (`windows_semaphore` already constructed in IPC memory).   
  
`windows_condition` internally uses an `windows_semaphore` called `m_sem_block_lock` to control access to the count of the number of threads blocked on the condition.  
  
When this `windows_semaphore` field is first constructed (by the parent `condition_ipc`), it obtains a corresponding Windows semaphore handle with the count initialized to 1 (as expected; it's essentially acting as a binary mutex that starts out unlocked):  
```  
handles.obtain_semaphore(this->id_, initialCount, &open_or_created);  
```  
  
When the process that created `condition_ipc` closes, assuming no other process has an active reference to it, that initial semaphore handle is deallocated by the Windows system, as expected (see [ECreateSemaphoreA Windows API doc](https://docs.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-createsemaphorea): "The semaphore object is destroyed when its last handle has been closed").  
  
The next time the process opens, it finds the parent `condition_ipc` of this `windows_semaphore` already in IPC memory, so it does not run the `windows_semaphore` constructor again. At this point, the `windows_semaphore` hasn't yet obtained a native Windows semaphore handle. Everything's still as expected.  
  
The trouble starts when `windows_semaphore` *does* obtain the Windows semaphore handle. On the first call to `windows_semaphore::wait()` (eg: when the ` tries to wait for a new sample from a producer), it obtains the Windows semaphore handle, but this time the count is initialized to 0 (the source of the error):  
```  
winapi_semaphore_functions sem(handles.obtain_semaphore(this->id_, 0));  
```  
  
As a result, the `windows_semaphore::wait()` call unexpectedly blocks (since the semaphore count is initialize to 0 rather than 1 as expected) and the call to `timed_wait` blocks.  
  
That might not be so bad if some other thread could signal the helper semaphore eventually by calling `notify_all()` on the condition. However, any thread that tries to do this deadlocks itself while trying to lock the `mutex_ipc` mutex because the original thread that called `timed_wait` failed to release `mutex_ipc` before deadlocking on the `m_sem_block_lock` semaphore.   
  
The fix seems relatively easy, unless I'm missing something obvious: when `windows_semaphore` obtains a Windows semaphore handle, it should always provide the correct value for the initial count (1, in this case, rather than 0). If the semaphore doesn't exist, then it'll be created with the correct initial count. If it does exist already, then the handle to the existing semaphore will be used (again, see [CreateSemaphoreA Windows API docs](https://docs.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-createsemaphorea)) and I don't think the given initial count matters.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-11 12:00:50 UTC  
> Url: https://github.com/boostorg/interprocess/issues/123#issuecomment-777401024  

Many thanks for the report and the associated pull request. I've applied a patch based on your initial pull request.
