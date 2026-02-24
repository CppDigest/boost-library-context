# #44 Drone: llvm apt installation [Merged]

> Username: sdarwin  
> Created at: 2025-10-13 16:06:26 UTC  
> Updated at: 2025-10-15 07:40:18 UTC  
> Merged at: 2025-10-15 07:40:18 UTC  
> Closed at: 2025-10-15 07:40:18 UTC  
> Url: https://github.com/boostorg/bloom/pull/44  

- `apt-key` is deprecated  
- `curl` will retry on more conditions than `wget`  
- move debugging output $DRONE_STAGE_MACHINE to as early as possible in the script  
  
If this goes well and tests are passing, then it's worthwhile to copy over to any other repos also.    
  
btw @pdimov these small updates are helpful in .drone.jsonnet files.

---

## Comment 1

> Username: cppalliance-bot  
> Created_at: 2025-10-13 16:12:30 UTC  
> Url: https://github.com/boostorg/bloom/pull/44#issuecomment-3398159351  

An automated preview of the documentation is available at [https://44.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html](https://44.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html)  
  
If more commits are pushed to the pull request, the docs will rebuild at the same URL.  
  
2025-10-13 16:12:29 UTC

---

## Comment 2

> Username: joaquintides  
> Created_at: 2025-10-14 16:31:57 UTC  
> Updated_at: 2025-10-14 16:32:21 UTC  
> Url: https://github.com/boostorg/bloom/pull/44#issuecomment-3402727345  

PR is working. Shall I merge in light of the parallel discussion going on at https://github.com/boostorg/unordered/pull/323?

---

## Comment 3

> Username: sdarwin  
> Created_at: 2025-10-14 17:09:43 UTC  
> Url: https://github.com/boostorg/bloom/pull/44#issuecomment-3402860372  

> Shall I merge in light of the parallel discussion  
  
These are small improvements aimed at apt.llvm.org. As long as that's in the drone file, this should be merged.  ( And, the debugging variables).      
Another separate step could be to remove apt.llvm.org entirely, which is Peter's idea.    
My opinion is... merge both the PRs, which adds the debugging. And a forward step for llvm.   
  
Then optionally, in another PR, refactor it.

---
