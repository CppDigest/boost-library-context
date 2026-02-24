# #72 - extra semicolon warning [Closed]

> Username: chybz  
> Created at: 2021-03-24 12:36:10 UTC  
> Updated at: 2021-04-09 07:04:32 UTC  
> Closed at: 2021-04-09 07:04:09 UTC  
> Url: https://github.com/boostorg/pfr/issues/72  

Hi,  
  
GCC 10.2 reports the following warning under `-Wpedantic` (1 of 3 pasted below):  
  
```  
boost/pfr/detail/fields_count.hpp:34:6: warning: extra ‘;’ [-Wpedantic]  
   34 |     };  
      |      ^  
```  
  
Thank you for this software masterpiece.

---

## Comment 1

> Username: apolukhin  
> Created at: 2021-04-09 07:04:32 UTC  
> Url: https://github.com/boostorg/pfr/issues/72#issuecomment-816462791  

Thanks for the report!  
  
Please, indicate all the lines with warnings next time
