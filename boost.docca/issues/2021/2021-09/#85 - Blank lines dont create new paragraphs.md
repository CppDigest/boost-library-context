# #85 - Blank lines dont create new paragraphs [Open]

> Username: vinniefalco  
> Created at: 2021-09-12 15:46:36 UTC  
> Updated at: 2021-09-12 15:46:36 UTC  
> Url: https://github.com/boostorg/docca/issues/85  

For example:  
```  
/** brief  
  
    paragraph 1  
  
    paragraph 2  
*/  
```  
  
does not produce two paragraphs as it should, per Markdown spec:  
  
![image](https://user-images.githubusercontent.com/1503976/132994048-491d8e53-b44f-472d-a64c-c4e5ce1cd238.png)
