# #367 - clang-pch actually uses PTH [Closed]

> Username: erichkeane  
> Created at: 2018-11-14 20:50:47 UTC  
> Updated at: 2024-06-11 13:05:00 UTC  
> Closed at: 2024-06-11 13:05:00 UTC  
> Url: https://github.com/boostorg/build/issues/367  

Clang has PreCompiledHeaders and PreTokenizedHeaders, however in PCH mode, boost build uses PTH.  
  
PTH is an un-documented(from a user perspective) failed experimental feature that has a pretty severe error in it that will result in Boost failing in PCH mode on clang.  As a result (and since Build seems to be the only user of PTH), I've been encouraged on the clang compiler to remove PTH support, which I'm in the process of doing now.  
  
I suggest that the Boost Build project switch to true PCH if possible.  
  
References to PTH (both include and emit):   
https://github.com/boostorg/build/blob/develop/src/tools/clang-linux.jam#L105  
https://github.com/boostorg/build/blob/develop/src/tools/clang-linux.jam#L128  
https://github.com/boostorg/build/blob/develop/src/tools/clang-linux.jam#L140  
https://github.com/boostorg/build/blob/develop/src/tools/clang-linux.jam#L148  
  
I also note that the file seems to refer to PTH and PCH interchangeably (99 calls it without-ptc and line 103 calls it 'with-pch', so that perhaps could be cleaned up as well.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:21 UTC  
> Url: https://github.com/boostorg/build/issues/367#issuecomment-859203492  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
