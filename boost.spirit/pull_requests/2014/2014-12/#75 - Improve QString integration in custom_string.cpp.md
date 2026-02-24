# #75 Improve QString integration in custom_string.cpp [Merged]

> Username: mloskot  
> Created at: 2014-12-15 10:54:08 UTC  
> Updated at: 2014-12-15 10:56:20 UTC  
> Merged at: 2014-12-15 10:56:20 UTC  
> Closed at: 2014-12-15 10:56:20 UTC  
> Url: https://github.com/boostorg/spirit/pull/75  

Improve QString integration in custom_string.cpp  
There is an example of how to use Qi with Qt's QString as a custom string.  
This is very helpful but it doesn't work in conjunction with the debug facilities.  
The following patch fixes this by adding a couple of template specialisations.  
  
Patch originally submitted as https://svn.boost.org/trac/boost/ticket/8846

---

## Comment 1

> Username: djowel  
> Created_at: 2014-12-15 10:56:09 UTC  
> Url: https://github.com/boostorg/spirit/pull/75#issuecomment-66978120  

Great! Thanks!

---
