# #398 Support newer version of Sphinx [Merged]

> Username: sdarwin  
> Created at: 2022-10-05 21:22:46 UTC  
> Updated at: 2024-10-10 13:29:00 UTC  
> Merged at: 2024-10-10 13:29:00 UTC  
> Closed at: 2024-10-10 13:29:00 UTC  
> Url: https://github.com/boostorg/python/pull/398  

Hi,  
The current boostorg/boost bundle has been created using Sphinx version 1.5.6   
  
If upgrading _all_ packages and Sphinx changes to 5.2.1, the numpy docs are slightly broken.   
  
- Search doesn't work    
- bullet lists are widely spaced     
  
Researched the problems in https://github.com/sphinx-doc/sphinx/issues . After some experimentation it looks like the fix in this pull request solves the issues.

---

## Comment 1

> Username: sdarwin  
> Created_at: 2023-01-10 19:30:35 UTC  
> Url: https://github.com/boostorg/python/pull/398#issuecomment-1377747279  

I tested these changes, and they worked for Sphinx 1.5.6 and 5.2.1. An easy way to verify is to merge this into the develop branch. New docs will appears at https://www.boost.org/doc/libs/develop/libs/python/doc/html/index.html .   If there are any problems it could be rolled back and not merged into the master branch.  
  
Only a small number of boost libraries have any issues, such as this one. Most do not use Sphinx.  After this update, it will open the door to upgrade the entire set of packages for all boost docs.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2023-02-23 21:27:40 UTC  
> Url: https://github.com/boostorg/python/pull/398#issuecomment-1442454627  

Sphinx 5.2.1 has been deployed in the boost develop branch. Not yet in the master branch. Example: https://www.boost.org/doc/libs/develop/libs/python/doc/html/numpy/reference/dtype.html

---
