# #32 Add check for segment_holding() failure [Closed]

> Username: ya1gaurav  
> Created at: 2015-10-20 08:12:40 UTC  
> Updated at: 2016-11-26 16:49:54 UTC  
> Closed at: 2016-11-26 16:49:54 UTC  
> Url: https://github.com/boostorg/container/pull/32  

segment_holding can return 0, in that case dereferencing oldsp will cause crash.  
assert added for Null return.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2015-10-24 19:35:59 UTC  
> Url: https://github.com/boostorg/container/pull/32#issuecomment-150842882  

An assert would not fix anything in release mode so we need to know if there is a real problem.Do you have experienced this failure (segment_holding returning null) or it is just due to a code review?  
  
There are some other places where segment_holding is assumed not to fail (e.g. sys_trim):  
  
```  
msegmentptr sp = segment_holding(m, (char*)m->top);  
if (!is_extern_segment(sp)) {  
```  
  
Another place (do_check_malloc_state):  
  
```  
if (m->top != 0) {   /* check top chunk */  
  do_check_top_chunk(m, m->top);  
```  
  
where "do_check_top_chunk" just:  
  
```  
msegmentptr sp = segment_holding(m, (char*)p);  
```  
  
I have no deep knowledge of dlmalloc internals, but it seems that segment_holding always succeeds if passed address is m->top and top != 0. Could add more information to see if this is real problem?

---

## Comment 2

> Username: ya1gaurav  
> Created_at: 2015-10-26 02:17:01 UTC  
> Url: https://github.com/boostorg/container/pull/32#issuecomment-151002931  

Actually it is reported by a static analyzer tool.  
There i check like below line 3223  
  msegmentptr sp = segment_holding(m, (char_)p);  
  size_t  sz = p->head & ~INUSE_BITS; /_ third-lowest bit can be set! */  
  assert(sp != 0);  
So, missing at other places.

---

## Comment 3

> Username: igaztanaga  
> Created_at: 2016-11-26 16:49:54 UTC  
> Url: https://github.com/boostorg/container/pull/32#issuecomment-263073461  

Reviewing dlmalloc, code segment_holding can't return null so the static analyzer gives a false positive, because it sees a pointer and it does not know that it can't be null. Closing, as there is no issue and we don't want to modify an external library.

---
