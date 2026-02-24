# #46 - Configurable underlying container [Open]

> Username: psiha  
> Created at: 2024-06-10 11:40:43 UTC  
> Updated at: 2024-06-10 13:34:37 UTC  
> Url: https://github.com/boostorg/icl/issues/46  

Similary to #39 but rather use the same approach as Boost.Container does for its flat_* containers: simply add a template parameter for specifying the underlying container.  
(I for example strictly _need_ to use a flat/trivially persistable underlying container).  
  
https://lists.boost.org/Archives/boost//2021/09/251937.php
