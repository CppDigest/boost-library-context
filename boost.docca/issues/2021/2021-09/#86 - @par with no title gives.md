# #86 - @par with no title gives "???" [Open]

> Username: vinniefalco  
> Created at: 2021-09-12 15:50:26 UTC  
> Updated at: 2021-09-12 15:50:57 UTC  
> Url: https://github.com/boostorg/docca/issues/86  

This  
```  
@par  
paragraph1  
  
@par  
paragraph2  
```  
  
produces:  
  
![image](https://user-images.githubusercontent.com/1503976/132994170-5a955066-b968-4de1-b84f-9e0dd4739fd7.png)  
  
If we could use `@par` without a title as a workaround to denote a paragraph break, in case we can't get blank lines to work, this could be useful.
