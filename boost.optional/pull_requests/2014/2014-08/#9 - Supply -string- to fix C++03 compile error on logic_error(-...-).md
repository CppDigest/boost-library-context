# #9 Supply <string> to fix C++03 compile error on logic_error("...") [Merged]

> Username: k-satoda  
> Created at: 2014-08-27 17:49:12 UTC  
> Updated at: 2014-09-04 21:36:18 UTC  
> Merged at: 2014-09-04 21:36:17 UTC  
> Closed at: 2014-09-04 21:36:17 UTC  
> Url: https://github.com/boostorg/optional/pull/9  

This change makes the library usable (again) on C++03 standard library  
implementations where <stdexcept> doesn't imply inclusion of <string>,  
e.g. STLport.

---

## Comment 1

> Username: k-satoda  
> Created_at: 2014-08-27 18:07:49 UTC  
> Url: https://github.com/boostorg/optional/pull/9#issuecomment-53615525  

Filed as [Trac ticket #10413](https://svn.boost.org/trac/boost/ticket/10413).  
  
> FYI, there was a same issue ([#6788](https://svn.boost.org/trac/boost/ticket/6788), and [fix](https://github.com/boostorg/uuid/commit/3de05322f1db7d6dc67332459b5aad02001b9703)) for uuid library.

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2014-09-02 07:40:22 UTC  
> Url: https://github.com/boostorg/optional/pull/9#issuecomment-54118582  

Just to let you know that I am aware of the request. I will act upon it in a couple of days. Thanks.

---
