# #8 haiku: don't crash because of unsupported locale in libstdc++ [Closed]

> Username: jessicah  
> Created at: 2014-11-16 01:00:13 UTC  
> Updated at: 2015-03-28 00:10:17 UTC  
> Closed at: 2015-03-28 00:10:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/8  

See https://svn.boost.org/trac/boost/ticket/4688  
We do the same as on Mac OS X and assume the filesystem uses utf-8.  
  
Signed-off-by: Jessica Hamilton jessica.l.hamilton@gmail.com

---

## Comment 1

> Username: Beman  
> Created_at: 2015-03-28 00:10:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/8#issuecomment-87128249  

Hi Jessica,  
  
Thanks for your pull request!  
  
I ended up applying the pull request (#10) from Ahmed Charles, because it was against develop rather than master, so am closing this one (#8).  
  
In the future, please submit pull requests against master, or on a new feature branch if applicable.  
  
Thanks,  
  
--Beman

---
