# #6 Fix implicit conversion changes signedness problems. [Merged]

> Username: 0-wiz-0  
> Created at: 2014-05-16 14:42:28 UTC  
> Updated at: 2014-07-01 02:34:48 UTC  
> Merged at: 2014-05-16 17:46:05 UTC  
> Closed at: 2014-05-16 17:46:05 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/6  

clang-3.5 trunk 201163 and OS X's clang (not sure which version)  
report -Wsign-conversion warnings with this header. This patch fixes  
them.  
  
https://svn.boost.org/trac/boost/ticket/9725  
  
Signed-off-by: Thomas Klausner tk@giga.or.at

---

## Comment 1

> Username: ahmedcharles  
> Created_at: 2014-05-16 20:34:50 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/6#issuecomment-43376368  

Note, this should be merged to develop first before merging to master. Please don't merge pull requests that are against develop without first rebasing them on top of develop.

---

## Comment 2

> Username: Belcourt  
> Created_at: 2014-05-16 20:37:38 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/6#issuecomment-43376627  

Yup, my bad.  I usually check that first and spaced it this time, sorry about that.

---
