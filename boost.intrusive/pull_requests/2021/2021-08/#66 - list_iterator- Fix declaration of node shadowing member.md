# #66 list_iterator: Fix declaration of node shadowing member [Merged]

> Username: jplcz  
> Created at: 2021-08-16 08:40:17 UTC  
> Updated at: 2021-11-17 14:54:15 UTC  
> Merged at: 2021-11-17 14:54:08 UTC  
> Closed at: 2021-11-17 14:54:08 UTC  
> Url: https://github.com/boostorg/intrusive/pull/66  

When code is compiled with -Wshadow (outside GCC's "system" include  
paths), the compiler will complain:  
  
```  
warning: declaration of ‘node’ shadows a member of ‘boost::intrusive::list_iterator<ValueTraits, IsConst>’  
```  
  
Example test:  
  
```  
g++ doc_list.cpp -o doc_list -I../include -Wshadow -Wall -Wextra -Werror  
```

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2021-11-17 14:54:15 UTC  
> Url: https://github.com/boostorg/intrusive/pull/66#issuecomment-971658271  

thanks for the patch!

---
