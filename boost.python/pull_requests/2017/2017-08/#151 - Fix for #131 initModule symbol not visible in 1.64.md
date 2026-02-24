# #151 Fix for #131 initModule symbol not visible in 1.64 [Closed]

> Username: formiaczek  
> Created at: 2017-08-22 14:29:15 UTC  
> Updated at: 2017-10-09 08:02:09 UTC  
> Closed at: 2017-10-09 08:02:09 UTC  
> Url: https://github.com/boostorg/python/pull/151  

If a global visibility is set to 'hidden', importing modules that define classes without either 'noimport' or 'init' causes   
errors like:  
`ImportError: dynamic module does not define init function`  
  
This seems to be due to some, probably unintended changes from #1.

---

## Comment 1

> Username: micbou  
> Created_at: 2017-09-30 08:43:47 UTC  
> Url: https://github.com/boostorg/python/pull/151#issuecomment-333294203  

Contrarily to PR https://github.com/boostorg/python/pull/159, this doesn't fix the issue when adding the `-fvisibility=hidden` flag to our project. See PR https://github.com/Valloric/ycmd/pull/848.

---
