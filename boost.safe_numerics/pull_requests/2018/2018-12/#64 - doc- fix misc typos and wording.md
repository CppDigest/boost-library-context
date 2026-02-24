# #64 doc: fix misc typos and wording [Closed]

> Username: d3fault  
> Created at: 2018-12-12 21:55:42 UTC  
> Updated at: 2018-12-12 22:33:29 UTC  
> Closed at: 2018-12-12 22:33:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/64  



---

## Comment 1

> Username: robertramey  
> Created_at: 2018-12-12 22:33:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/64#issuecomment-446769662  

OK - not bad for a first try.  Alas - there's always more to it.  
  
The document sources are in the files in the directory doc/docbook.  These are XML format files.  I edit them using XMLMind but then can be just as well be edited by hand.  From these files the macros makehtml, makepdf, etc venerate the html and pdf versions of the documentation.  So editing the html files won't last and changes won't be transmitted to the pdf versions of the docs.  You're welcome to make the changes in the docbook directory and I'll merth them in

---
