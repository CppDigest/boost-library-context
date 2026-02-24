# #18 Workaround gcc 9-12 code miscompilation that broke section link ids [Merged]

> Username: Lastique  
> Created at: 2022-09-24 12:36:57 UTC  
> Updated at: 2022-09-26 16:19:16 UTC  
> Merged at: 2022-09-26 15:14:48 UTC  
> Closed at: 2022-09-26 15:14:48 UTC  
> Url: https://github.com/boostorg/quickbook/pull/18  

gcc 9.2 through 12.1 has a code generation bug that result in incorrect link ids for some sections in the generated XML.  
  
This commit applies a workaround as suggested by Nikita Kniazev in:  
  
https://github.com/boostorg/spirit/issues/737#issuecomment-1256830393  
  
Fixes https://github.com/boostorg/quickbook/issues/11.

---
