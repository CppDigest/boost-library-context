# #2 Remove explicit permissions on file open. [Closed]

> Username: cpatrick  
> Created at: 2014-03-07 20:40:11 UTC  
> Updated at: 2016-12-06 19:52:27 UTC  
> Closed at: 2016-12-06 19:52:23 UTC  
> Url: https://github.com/boostorg/iostreams/pull/2  

In mapped_file, there are explicity S_IRWXU permissions on file open. This ignores umask and that can produce an unexpected 700 permission on files created via mapped files.  
  
https://svn.boost.org/trac/boost/ticket/7273

---

## Comment 1

> Username: cpatrick  
> Created_at: 2014-03-07 20:45:39 UTC  
> Url: https://github.com/boostorg/iostreams/pull/2#issuecomment-37065804  

Also, if there's a better way to contribute this, please let me know.

---

## Comment 2

> Username: eldiener  
> Created_at: 2016-12-03 23:55:36 UTC  
> Url: https://github.com/boostorg/iostreams/pull/2#issuecomment-264674020  

PR are never made against the 'master' branch.

---

## Comment 3

> Username: eldiener  
> Created_at: 2016-12-06 19:38:22 UTC  
> Url: https://github.com/boostorg/iostreams/pull/2#issuecomment-265250461  

This is still against the 'master' branch.

---

## Comment 4

> Username: cpatrick  
> Created_at: 2016-12-06 19:52:23 UTC  
> Url: https://github.com/boostorg/iostreams/pull/2#issuecomment-265254493  

This was created over two years ago, so I'm just going to close it. I do hope that bug gets fixed one day (if it hasn't already).

---
