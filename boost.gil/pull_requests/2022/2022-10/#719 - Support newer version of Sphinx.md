# #719 Support newer version of Sphinx [Merged]

> Username: sdarwin  
> Created at: 2022-10-04 22:59:57 UTC  
> Updated at: 2022-10-05 09:16:45 UTC  
> Merged at: 2022-10-05 09:16:45 UTC  
> Closed at: 2022-10-05 09:16:45 UTC  
> Url: https://github.com/boostorg/gil/pull/719  

Hi,  
  
The current boostorg/boost bundle has been created using Sphinx version 1.5.6   
  
If upgrading _all_ packages and Sphinx changes to 5.2.1 , the gil docs are slightly broken.   
  
- Search doesn't work    
- bullet lists are widely spaced     
  
Researched the problems in https://github.com/sphinx-doc/sphinx/issues . After some experimentation, it looks like the fix in this pull request solves the issues.

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2022-10-05 09:16:30 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/719#pullrequestreview-1131127271  

Awesome, thank you!

---
