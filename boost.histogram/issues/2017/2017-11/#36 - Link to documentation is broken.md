# #36 - Link to documentation is broken [Closed]

> Username: veprbl  
> Created at: 2017-11-22 20:47:25 UTC  
> Updated at: 2017-11-23 16:50:25 UTC  
> Closed at: 2017-11-23 16:50:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/36  

From the README:  
```  
Check out the full documentation. Highlights are given below.  
```  
The link now points to https://htmlpreview.github.io/?https://raw.githubusercontent.com/HDembinski/histogram/html/doc/html/index.html  
which is a working page, but none of the links on the page itself work for me (I've tried bunch of browsers), so I can't get to any documentation text.  
  
Would you consider using gh-pages? I've checked and they render documentation just fine for me: https://veprbl.github.io/histogram/doc/html/ All you need to do the switch is to rename your html branch into gh-pages and update README of course.

---

## Comment 1

> Username: HDembinski  
> Created at: 2017-11-23 16:50:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/36#issuecomment-346664500  

Hi, yeah, somehow it is a problem with htmlpreview. I just moved the docs to gh-pages, like you suggested. Thank you!
