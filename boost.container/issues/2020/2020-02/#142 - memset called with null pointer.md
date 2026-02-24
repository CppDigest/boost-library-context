# #142 - memset called with null pointer [Closed]

> Username: rainerdeyke  
> Created at: 2020-02-09 12:03:54 UTC  
> Updated at: 2020-02-14 04:15:58 UTC  
> Closed at: 2020-02-14 04:14:59 UTC  
> Url: https://github.com/boostorg/container/issues/142  

When creating a boost::container::vector with an explicit size of 0, std::memset is called with a null pointer as the first argument (and a length of 0).  It is my understanding that this is undefined behavior, and indeed, if I compile with -fsanitize=undefined, I get a runtime error that looks like this:  
`/home/rainer/projects/starfire/intermediate/rainer10/installed/linux64-local/include/boost/container/detail/copy_move_algo.hpp:590:15: runtime error: null pointer passed as argument 1, which is declared to never be null  
`  
I am using Boost 1.72 on Lubuntu 18.04 LTS.  I have tested with multiple compilers, and all report the same problem.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-02-14 04:15:43 UTC  
> Updated at: 2020-02-14 04:15:58 UTC  
> Url: https://github.com/boostorg/container/issues/142#issuecomment-586089872  

Fixed in commit git commit:  
  
https://github.com/boostorg/container/commit/756c4cdc6a44bff0908a64078054a585b8cbdff8  
  
Many thanks for the report!
