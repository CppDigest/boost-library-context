# #124 Fix deadlock for interprocess_condition in IPC memory on Windows [Closed]

> Username: bhumbers  
> Created at: 2020-07-31 15:02:18 UTC  
> Updated at: 2021-02-11 12:02:18 UTC  
> Closed at: 2021-02-11 12:02:17 UTC  
> Url: https://github.com/boostorg/interprocess/pull/124  

Resolves https://github.com/boostorg/interprocess/issues/123  
  
On Windows, when obtaining a semaphore handle from the system, always provide the correct initial count. Previously, the initial count was only being given when obtaining a semaphore handle in the constructor, but this should be used consistently whenever the handle is obtained.  
  
The particular case where this matters is when an `interprocess_condition` (which uses helper semaphores via `windows_condition`) is stored in shared memory that persists between process runs. After creating the `interprocess_condition` in the first run in shared memory, the semaphore handle is disposed on process exit, even though the `interprocess_condition` continues to live in shared mem.  
  
On the second run of a process that accesses `interprocess_condition`, it must re-obtain the handle, but previously it was using initial count 0 rather than the expected 1 (see `boost::interprocess::ipcdetail::windows_condition::condition_data::m_sem_block_lock`, which uses initial count 1 to act as a binary mutex).

---

## Comment 1

> Username: bhumbers  
> Created_at: 2020-08-06 12:58:18 UTC  
> Url: https://github.com/boostorg/interprocess/pull/124#issuecomment-669910659  

Issue: This PR changes the size of `windows_semaphore`, which causes problems when a semaphore is put in IPC memory & shared by code compiled before & after this change.   
  
Fix is likely to template `windows_semaphore` on the initial count rather than adding an `initialCount_` member. Updated commit coming soon for that.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2021-02-11 12:02:17 UTC  
> Url: https://github.com/boostorg/interprocess/pull/124#issuecomment-777401890  

Many thanks for the pull, fixed in commit:  
  
https://github.com/boostorg/interprocess/commit/86464712c21db3142c15d1fa02f444dcc0838fa5

---
