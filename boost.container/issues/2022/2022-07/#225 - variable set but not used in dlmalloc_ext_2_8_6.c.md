# #225 - variable set but not used in dlmalloc_ext_2_8_6.c [Closed]

> Username: jefftrull  
> Created at: 2022-07-26 00:59:35 UTC  
> Updated at: 2023-04-18 21:12:40 UTC  
> Closed at: 2023-04-18 21:12:40 UTC  
> Url: https://github.com/boostorg/container/issues/225  

The function `boost_cont_allocated_memory()` initializes and periodically updates a local variable `sum`, but never uses it for anything. This triggers warnings in some compilers and static analysis tools:  
  
```c++  
      size_t sum = mfree;          // ===> initialization  
      msegmentptr s = &m->seg;  
      while (s != 0) {  
         mchunkptr q = align_as_chunk(s->base);  
         while (segment_holds(s, q) &&  
               q != m->top && q->head != FENCEPOST_HEAD) {  
            size_t sz = chunksize(q);  
            sum += sz;             // ===> update  
  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-09-22 06:50:03 UTC  
> Url: https://github.com/boostorg/container/issues/225#issuecomment-1254602459  

Thanks for the report, but I can't see how it's not used, "sum" is then assigned to nm.arena, which is returned  
``nm.arena    = sum;``

---

## Comment 2

> Username: jefftrull  
> Created at: 2022-09-22 18:13:52 UTC  
> Updated at: 2022-09-22 18:14:10 UTC  
> Url: https://github.com/boostorg/container/issues/225#issuecomment-1255381864  

The code you reference is in `dlmalloc_2_8_6.c`, not `dlmalloc_ext_2_8_6.c`

---

## Comment 3

> Username: hdu-sdlzx  
> Created at: 2022-12-19 09:07:40 UTC  
> Url: https://github.com/boostorg/container/issues/225#issuecomment-1357321013  

@igaztanaga clang-15 complains about the following unused variables:  
libs/container/src/dlmalloc_2_8_6.c:3991:7: warning: variable 'nfences' set but not used [-Wunused-but-set-variable]  
  int nfences = 0;  
      ^  
In file included from libs/container/src/alloc_lib.c:24:  
libs/container/src/dlmalloc_ext_2_8_6.c:1227:14: warning: variable 'sum' set but not used [-Wunused-but-set-variable]  
      size_t sum = mfree;  
             ^  
2 warnings generated.
