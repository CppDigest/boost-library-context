# #10 Remove unnecessary "extern" on some inline functions [Merged]

> Username: k-satoda  
> Created at: 2015-07-17 17:18:52 UTC  
> Updated at: 2015-07-21 15:10:15 UTC  
> Merged at: 2015-07-21 15:10:15 UTC  
> Closed at: 2015-07-21 15:10:15 UTC  
> Url: https://github.com/boostorg/signals2/pull/10  

There is an ancient compiler that (wrongly) rejects "etern inline".  
http://am.renesas.com/products/tools/coding_tools/c_compilers_assemblers/sh_compiler/index.jsp  
  
The removal is also good for consistency. There is no other occurrence  
of "extern inline" throughout Boost source tree.

---
