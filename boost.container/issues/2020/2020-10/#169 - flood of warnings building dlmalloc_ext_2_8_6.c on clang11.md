# #169 - flood of warnings building dlmalloc_ext_2_8_6.c on clang11 [Closed]

> Username: vinniefalco  
> Created at: 2020-10-29 22:06:05 UTC  
> Updated at: 2020-10-31 18:19:15 UTC  
> Closed at: 2020-10-31 17:57:51 UTC  
> Url: https://github.com/boostorg/container/issues/169  

From https://travis-ci.com/github/vinniefalco/json/jobs/420747907#L894  
  
```  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:38:  
libs/container/src/dlmalloc_2_8_6.c:3228:53: warning: arithmetic on a null pointer treated as a cast from integer to pointer is a GNU extension [-Wnull-pointer-arithmetic]  
  assert(sz == ((sp->base + sp->size) - (char*)p) - TOP_FOOT_SIZE);  
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~  
libs/container/src/dlmalloc_2_8_6.c:2729:17: note: expanded from macro 'TOP_FOOT_SIZE'  
  (align_offset(chunk2mem(0))+pad_request(sizeof(struct malloc_segment))+MIN_CHUNK_SIZE)  
                ^  
libs/container/src/dlmalloc_2_8_6.c:2216:55: note: expanded from macro 'chunk2mem'  
#define chunk2mem(p)        ((void*)((char*)(p)       + TWO_SIZE_T_SIZES))  
                                                      ^  
libs/container/src/dlmalloc_2_8_6.c:1620:14: note: expanded from macro 'align_offset'  
 ((((size_t)(A) & CHUNK_ALIGN_MASK) == 0)? 0 :\  
             ^  
libs/container/src/dlmalloc_2_8_6.c:1444:24: note: expanded from macro 'assert'  
#define assert(x) if(!(x)) ABORT  
                       ^  
libs/container/src/dlmalloc_2_8_6.c:3228:53: warning: arithmetic on a null pointer treated as a cast from integer to pointer is a GNU extension [-Wnull-pointer-arithmetic]  
  assert(sz == ((sp->base + sp->size) - (char*)p) - TOP_FOOT_SIZE);  
  ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~  
libs/container/src/dlmalloc_2_8_6.c:2729:17: note: expanded from macro 'TOP_FOOT_SIZE'  
  (align_offset(chunk2mem(0))+pad_request(sizeof(struct malloc_segment))+MIN_CHUNK_SIZE)  
                ^  
libs/container/src/dlmalloc_2_8_6.c:2216:55: note: expanded from macro 'chunk2mem'  
#define chunk2mem(p)        ((void*)((char*)(p)       + TWO_SIZE_T_SIZES))  
                                                      ^  
libs/container/src/dlmalloc_2_8_6.c:1621:34: note: expanded from macro 'align_offset'  
  ((MALLOC_ALIGNMENT - ((size_t)(A) & CHUNK_ALIGN_MASK)) & CHUNK_ALIGN_MASK))  
                                 ^  
libs/container/src/dlmalloc_2_8_6.c:1444:24: note: expanded from macro 'assert'  
#define assert(x) if(!(x)) ABORT  
                       ^  
libs/container/src/dlmalloc_2_8_6.c:3422:25: warning: arithmetic on a null pointer treated as a cast from integer to pointer is a GNU extension [-Wnull-pointer-arithmetic]  
    sum += m->topsize + TOP_FOOT_SIZE;  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-10-31 17:58:21 UTC  
> Url: https://github.com/boostorg/container/issues/169#issuecomment-719966987  

Thanks for the report. I don't have access to clang 11, please report if the commit does not fix all the warnings.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-31 18:19:15 UTC  
> Url: https://github.com/boostorg/container/issues/169#issuecomment-719969406  

Looks good now! See: https://travis-ci.com/github/vinniefalco/json/jobs/423314334
