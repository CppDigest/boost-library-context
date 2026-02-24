# #608 - bjam' always 'bus error' on NetBSD/sparc (presumably, other strict-alignement 32 bits arch as well) [Open]

> Username: rdolbeau  
> Created at: 2020-05-18 11:07:06 UTC  
> Updated at: 2021-05-29 17:22:40 UTC  
> Url: https://github.com/boostorg/build/issues/608  

Originally reported downstream at pkgsrc (http://gnats.netbsd.org/55269).  
  
# The observation:  
  
Trying to compile the "boost" meta-package in pkgsrc on NetBSD 9.0/sparc, 'bjam' always crashes with a 'bus error'. The culprit is the function `timestamp_init` in `tools/build/src/engine/timestamp.cpp`, which does:  
  
`time->secs = secs;`  
  
where `secs` are `time_t`, currently 8 bytes. This is only legal on SPARC if `time->secs` is 8-bytes aligned, which it isn't, so the 8-bytes store causes a 'bus error'. The reason for the nonalignment is that it comes from the hash table implementation in hash.cpp, which does:  
  
`#define hash_item_data(item) ((HASHDATA *)((char *)item + sizeof(ITEM)))`  
  
`ITEM` is the size of a pointer, and `item` is properly aligned (at least 8 bytes), so this is _never_ aligned properly with 32-bits pointers... This result in reliable crashes on 32-bits machine with strict alignment requirements (i.e., sparc).  
  
# my fix  
  
The following patch fixes the problem for me, but might be a bit overkill (memory wastage) on 32-bits architecture that supports unaligned access. It should be a no-op on 64-bits architecture.  
  
```  
--- tools/build/src/engine/hash.cpp.orig        2019-12-10 01:20:17.000000000 +0100  
+++ tools/build/src/engine/hash.cpp     2020-05-16 14:55:46.086210410 +0200  
@@ -33,8 +33,8 @@  
 typedef struct item ITEM;  
 struct item  
 {  
-    ITEM * next;  
-};  
+    ITEM * next __attribute__ ((aligned (8)));  
+} __attribute__ ((aligned (8)));  
  
 #define MAX_LISTS 32  
```  
There is probably better solution than that, that would generally respect alinement's rules on all platforms.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-05-18 15:01:07 UTC  
> Url: https://github.com/boostorg/build/issues/608#issuecomment-630241395  

Thanks for reporting this.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 17:22:10 UTC  
> Url: https://github.com/boostorg/build/issues/608#issuecomment-850868287  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
