# #54 - [REGRESSION] Pool size is extended on each allocation [Open]

> Username: orgads  
> Created at: 2023-06-22 09:52:20 UTC  
> Updated at: 2025-01-04 16:21:52 UTC  
> Url: https://github.com/boostorg/pool/issues/54  

Reported in Boost mailing list: https://lists.boost.org/Archives/boost/2023/06/254750.php  
  
951ca5725 changed malloc_need_resize() to use set_next_size(), which  
sets start_size in addition to setting next_size. this causes repeated use  
of purge_memory() to allocate 2x size after every use. an illustration:  
  
```c++  
#include <boost/pool/pool.hpp>  
int main()  
{  
  boost::pool pool(8);  
  for(int i = 0; i < 10; i++) {  
    printf("%d %ld\n", i, pool.get_next_size());  
    pool.purge_memory();  
    void *ptr = pool.malloc();  
    (void)ptr;  
  }  
}  
```  
  
before the commit the output is:  
```  
0 32  
1 64  
2 64  
3 64  
4 64  
5 64  
6 64  
7 64  
8 64  
9 64  
```  
after the commit, the output is:  
```  
0 32  
1 64  
2 128  
3 256  
4 512  
5 1024  
6 2048  
7 4096  
8 8192  
9 16384  
```

---

## Comment 1

> Username: joelhock  
> Created at: 2023-06-22 14:31:20 UTC  
> Url: https://github.com/boostorg/pool/issues/54#issuecomment-1602746331  

thank you for the quick fix!!
