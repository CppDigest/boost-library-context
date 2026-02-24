# #3 Don't delete copy constructor when rvalue references are disabled [Merged]

> Username: MarcelRaad  
> Created at: 2014-09-23 13:38:07 UTC  
> Updated at: 2014-09-23 14:41:53 UTC  
> Merged at: 2014-09-23 14:39:25 UTC  
> Closed at: 2014-09-23 14:39:25 UTC  
> Url: https://github.com/boostorg/move/pull/3  

Before SVN r82706 from February 2013 (git a7091d1009f057b1148b36fabf91f83667db5f72), Boost.Move worked perfectly for compilers that supported deleted functions without supporting rvalue references (for example, clang with libstdc++ 4.2.1 on Mac to support OS X 10.6). r82706 broke those setups as temporaries now call the deleted copy constructor instead of the move constructor. This change reverts those setups to the old behavior.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-09-23 14:39:42 UTC  
> Url: https://github.com/boostorg/move/pull/3#issuecomment-56529855  

Thanks for the patch!

---
