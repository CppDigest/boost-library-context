# #66 reset next_size/start_size when calling purge_memory [Open]

> Username: mark05417  
> Created at: 2025-07-14 09:16:24 UTC  
> Updated at: 2025-07-14 13:21:29 UTC  
> Url: https://github.com/boostorg/pool/pull/66  

```  
int main() {  
    boost::pool<mark_new_delete> memory_pool(sizeof(int));  
      
    for(int i=0;i<40;i++){  
        // use malloc to get some memory from pool  
        int* data = static_cast<int*>(memory_pool.malloc());  
        int* data2 = static_cast<int*>(memory_pool.malloc());  
  
        // release all the memory to the system instead of calling memory_pool.free(data)  
        memory_pool.purge_memory();  
    }  
    return 0;  
}  
```  
  
Consider the above code, I think it is a normal scenario that users may use, it has the performance issue.  
  
Althought the more correct usage might be call `memory_pool.free()` to free those memory (data1, data2) one by one instead of calling `purge_memory()` at each iteration/rounds.  
  
The current doubling algorithm doesn't reset the start_size and the next_size. If calling `memory_pool.purge_memory()`, the pool releases memory to the system. And the next time `memory_pool.malloc()` is called, the pool asks for the doubled memory from system, which is not expected.

---
