# #99 - MacOS: Workaround for extremely slow performance creating lockfree::stack instances (99.999999% for sure a MacOS problem only) [Closed]

> Username: kmatheussen  
> Created at: 2023-12-30 10:59:57 UTC  
> Updated at: 2023-12-30 11:00:45 UTC  
> Closed at: 2023-12-30 11:00:45 UTC  
> Url: https://github.com/boostorg/lockfree/issues/99  

Motivation for adding this ticket: Just for the record, in case anyone else searches for a solution to this issues.  
  
Problem: At least on MacOS 14, creating 16 lockfree:stack instances sequentially takes more than 10 seconds(!) on an M3 macbook.  
  
A solution:  
  
```  
#define NUM_POOLS 16  
#define POOL boost::lockfree::stack<RT_mempool_data*>  
+  std::thread t[NUM_POOLS];  
+    
+  for(int i=0;i<NUM_POOLS;i++)  
+  {  
+         // Allocate each pool in it's own thread. Workaround for really slow memory allocation performance on Macos.  
+         t[i] = std::thread([i](){          
+                 g_pools[i] = new POOL(MAX_POOL_SIZE);  
+         });  
+  }  
+  
   for(int i=0;i<NUM_POOLS;i++)  
-    g_pools[i] = new POOL(MAX_POOL_SIZE);  
+         t[i].join();  
  
```

---

## Comment 1

> Username: kmatheussen  
> Created at: 2023-12-30 11:00:45 UTC  
> Url: https://github.com/boostorg/lockfree/issues/99#issuecomment-1872505248  

Closing since it's a bug in MacOS and not in Boost/Lockfree.
