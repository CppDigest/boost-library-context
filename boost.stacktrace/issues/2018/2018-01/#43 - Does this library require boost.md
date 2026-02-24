# #43 - Does this library require boost? [Closed]

> Username: Sunhick  
> Created at: 2018-01-16 04:26:53 UTC  
> Updated at: 2018-01-16 08:15:18 UTC  
> Closed at: 2018-01-16 08:15:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/43  

I'm trying to use stacktrace on android without boost library and i get   
  
"frame_unwind.ipp:10:28: fatal error: boost/config.hpp: No such file or directory".

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-01-16 08:15:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/43#issuecomment-357884262  

Yes, it requires Boost.  
  
But you can always extract a library from Boost using BCP http://www.boost.org/doc/libs/1_66_0/tools/bcp/doc/html/index.html
