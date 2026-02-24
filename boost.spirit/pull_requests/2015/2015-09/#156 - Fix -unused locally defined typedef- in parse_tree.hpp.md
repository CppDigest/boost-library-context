# #156 Fix "unused locally defined typedef" in parse_tree.hpp [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2015-09-23 10:03:44 UTC  
> Updated at: 2015-09-23 10:16:50 UTC  
> Merged at: 2015-09-23 10:16:50 UTC  
> Closed at: 2015-09-23 10:16:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/156  

Hi,  
  
When building against Spirit with -Wall -Wextra and gcc 4.9, we get:  
  
79:42: error: typedef 'attr_t' locally defined but not used [-Werror=unused-local-typedefs]  
typedef typename match_t::attr_t attr_t;  
  
in file include/boost/spirit/home/classic/tree/parse_tree.hpp.  
  
This patch fixes it.  
  
Cheers,  
Romain

---
