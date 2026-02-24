# #15 fix uninitialized variable and close(-1) behavior [Merged]

> Username: ganboing  
> Created at: 2017-09-12 19:41:19 UTC  
> Updated at: 2017-10-22 19:41:54 UTC  
> Merged at: 2017-10-22 19:41:54 UTC  
> Closed at: 2017-10-22 19:41:54 UTC  
> Url: https://github.com/boostorg/process/pull/15  

Valgrind complains about:  
  
==18274== Conditional jump or move depends on uninitialised value(s)  
==18274==    at 0x4E63291: boost::process::child::_exited() (child_decl.hpp:54)  
==18274==    by 0x4E634B0: boost::process::child::~child() (child_decl.hpp:93)  
  
==18274==  Uninitialised value was created by a stack allocation  
==18274==    at 0x4E6351A: boost::process::child::running() (child_decl.hpp:102)  
  
Line numbers refer to ver 1.65.1  
  
Not sure what should be a proper fix for this. Initialize "code" as -1 for now.  
  
Valgrind also complains about close(-1) in basic_pipe.hpp. Should not close if _sink or _source is -1.

---
