# #35 Add support for headers in first level sublibs [Closed]

> Username: Lastique  
> Created at: 2014-08-31 09:06:47 UTC  
> Updated at: 2014-09-01 11:01:25 UTC  
> Closed at: 2014-09-01 10:41:47 UTC  
> Url: https://github.com/boostorg/boost/pull/35  

The sub-libraries that reside in the first level subdirectories in the library git module can now have headers inside include/boost subdirectories. 'b2 headers' will now link those headers into the global tree.  
  
This makes the hack for Boost.Numeric obsolete.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-09-01 10:41:47 UTC  
> Url: https://github.com/boostorg/boost/pull/35#issuecomment-54045347  

Thanks for the patch, it has been merged.  
  
I have reformatted the commit message to fit in 80 columns, and added the links to discussions of sublib thing, which you gave on the mailist list.

---
