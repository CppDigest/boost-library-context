# #57 - Document supported boost version [Closed]

> Username: david-grs  
> Created at: 2018-10-28 12:56:28 UTC  
> Updated at: 2018-12-06 19:50:40 UTC  
> Closed at: 2018-12-06 19:50:40 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/57  

Hey Robert,  
  
Thanks for your library - I was trying to use it with g++ 7.3.0 and it took me a while to realize I missed this fix from boost.logic: https://github.com/boostorg/logic/pull/5/files  
  
I think it would be great to document (in the README?) that this fix is needed - at least for some versions of gcc.   
  
Thanks!  
  
Cheers  
David

---

## Comment 1

> Username: robertramey  
> Created at: 2018-12-06 19:50:40 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/57#issuecomment-445006455  

the official version is being released for the first time - today?  It should work with versions of g++ > 5 without issues.  But since it's the first time, by definition - we'll have surprises
