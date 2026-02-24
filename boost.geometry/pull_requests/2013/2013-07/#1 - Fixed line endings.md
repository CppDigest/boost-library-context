# #1 Fixed line endings [Closed]

> Username: vbudovski  
> Created at: 2013-07-27 15:08:51 UTC  
> Updated at: 2014-06-14 02:05:40 UTC  
> Closed at: 2013-07-28 15:40:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1  



---

## Comment 1

> Username: dabrahams  
> Created_at: 2013-07-28 15:40:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1#issuecomment-21685365  

Thanks for the patch, but the automatic conversion process is just going to clobber anything we do in these repositories.  The place to fix problems like this one is in https://github.com/ryppl/Boost2Git/blob/master/dot_gitattributes  
  
See http://git-scm.com/book/ch7-2.html and/or https://www.kernel.org/pub/software/scm/git/docs/gitattributes.html for details. Would you mind submitting a patch for that file?

---

## Comment 2

> Username: ned14  
> Created_at: 2013-08-11 10:09:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1#issuecomment-22455402  

I looked into this:  
  
```  
ned@jenkins:~/trunk/libs/geometry/example$ svn proplist ml02_distance_strategy.cpp  
Properties on 'ml02_distance_strategy.cpp':  
  svn:mime-type  
ned@jenkins:~/trunk/libs/geometry/example$ svn proplist ml01_multipolygon_simplify.cpp  
Properties on 'ml01_multipolygon_simplify.cpp':  
  svn:mime-type  
  svn:eol-style  
ned@jenkins:~/trunk/libs/geometry/example$ svn proplist ml02_distance_strategy.vcproj  
Properties on 'ml02_distance_strategy.vcproj':  
  svn:mime-type  
ned@jenkins:~/trunk/libs/geometry/example$ svn proplist ml01_multipolygon_simplify.vcproj  
Properties on 'ml01_multipolygon_simplify.vcproj':  
  svn:mime-type  
  svn:eol-style  
```  
  
There is clearly a missing svn:eol-style in the original.  
  
Niall

---
