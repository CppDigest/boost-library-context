# #158 atomic.hpp: adapt headers to AIX assembler [Merged]

> Username: Helflym  
> Created at: 2021-11-02 10:21:56 UTC  
> Updated at: 2021-11-07 20:27:53 UTC  
> Merged at: 2021-11-07 20:27:46 UTC  
> Closed at: 2021-11-07 20:27:46 UTC  
> Url: https://github.com/boostorg/interprocess/pull/158  

AIX assembler doesn't allow "bne- 1b" or "bne- 1f" symtax to jump to a  
given label. It must be replaced by "bne- $N", N being the memory  
offset between the current instruction and the targeted label.  
  
Equivalent PR in boost/atomic: https://github.com/boostorg/atomic/pull/50

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-11-07 20:27:53 UTC  
> Url: https://github.com/boostorg/interprocess/pull/158#issuecomment-962675484  

Many thanks for the patch!

---
