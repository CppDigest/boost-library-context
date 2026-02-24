# #86 - Documentation issues on boost.org [Closed]

> Username: cstratopoulos  
> Created at: 2018-08-09 19:53:48 UTC  
> Updated at: 2018-11-13 03:18:04 UTC  
> Closed at: 2018-11-13 03:18:04 UTC  
> Url: https://github.com/boostorg/yap/issues/86  

Hello  
  
Not sure if this is the right place to bring this up, but I was just skimming the YAP documentation on boost.org and I noticed a couple issues.  
  
From the index page: https://www.boost.org/doc/libs/1_68_0/doc/html/yap.html  
  
The "Reference" TOC entry links to  https://www.boost.org/doc/libs/1_68_0/doc/html/reference.html which appears to be the page for the BoostBook documentation reference. This seems to have messed up all the sub-headings (`algorithm.hpp`, `config.hpp`, etc.) as well.  
  
I'm also noticing some missing images when comparing  
  
https://www.boost.org/doc/libs/1_68_0/doc/html/boost_yap/manual.html#boost_yap.manual.an_expression_template_primer  
  
to  
  
https://boostorg.github.io/yap/doc/html/boost_yap/manual/an_expression_template_primer.html  
  
In the boost.org version it looks like `ast.png` and `expr.png` are not found, rendering just as the text `ast` and `expr`  
  
I'm also seeing basically no entries under the class/function/macro index, see  
  
https://www.boost.org/doc/libs/1_68_0/doc/html/yap.html#id-1.3.48.9  
  
vs, e.g.,  
  
https://boostorg.github.io/yap/doc/html/index/s07.html

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-11-13 03:18:04 UTC  
> Url: https://github.com/boostorg/yap/issues/86#issuecomment-438117390  

Fixed in 1.69.
