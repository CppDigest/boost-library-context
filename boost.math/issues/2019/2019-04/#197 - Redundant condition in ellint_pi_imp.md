# #197 - Redundant condition in ellint_pi_imp? [Closed]

> Username: shaneasd  
> Created at: 2019-04-19 04:32:55 UTC  
> Updated at: 2019-05-13 18:16:28 UTC  
> Closed at: 2019-05-13 18:16:14 UTC  
> Url: https://github.com/boostorg/math/issues/197  

In 47c2f9254cb82b8cc64b570e077ad90ce8317cce this code was added  
```  
   if(v == 1)  
   {  
      ...  
      return result;  
   }  
```  
Later in the method the following code appears  
  
```  
   if(k == 0)  
   {  
      // A&S 17.7.20:  
      if(v < 1)  
      {  
         T vcr = sqrt(vc);  
         return atan(vcr * tan(phi)) / vcr;  
      }  
      else if(v == 1)  
      {  
         return tan(phi);  
      }  
```  
It seems to me that that ``` else if(v == 1)``` will never be true because if it were, the first check for v==1 would have been hit.  
  
The simplest solution would be to recognize that this code will not execute and remove it. However the expression it uses for k=0,v=1 seems a lot simpler than the expression being used currently so perhaps it would be better to rearrange the cases.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-05-13 18:16:28 UTC  
> Url: https://github.com/boostorg/math/issues/197#issuecomment-491930400  

Good spot, thanks for that!
