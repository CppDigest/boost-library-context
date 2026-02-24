# #1 Fixed line endings [Closed]

> Username: vbudovski  
> Created at: 2013-07-27 15:20:19 UTC  
> Updated at: 2013-09-11 10:16:46 UTC  
> Closed at: 2013-07-28 15:40:52 UTC  
> Url: https://github.com/boostorg/boost/pull/1  



---

## Comment 1

> Username: dabrahams  
> Created_at: 2013-07-28 15:40:52 UTC  
> Url: https://github.com/boostorg/boost/pull/1#issuecomment-21685371  

https://github.com/boostorg/geometry/pull/1#issuecomment-21685365 applies here as well

---

## Comment 2

> Username: ned14  
> Created_at: 2013-08-11 10:35:48 UTC  
> Url: https://github.com/boostorg/boost/pull/1#issuecomment-22455714  

I looked into this:  
  
Firstly:  
  
```  
ned@jenkins:~/trunk$ svn proplist doc/test/test.svg  
Properties on 'doc/test/test.svg':  
  svn:mime-type  
```  
  
Same problem as earlier: missing correct svn:eol-style.  
  
Secondly:  
  
Doing a git clone https://github.com/boostorg/boost.git on my windows desktop here and a git commit -a yields working directory clean.  
  
Doing the same on Linux however and bootstrap.bat and doc/test/test.svg are modified. The latter we know about, the former is because .gitattributes says .bat ought to be native EOL while SVN props for that file says eol-style=CRLF, despite that https://svn.boost.org/trac/boost/wiki/BoostSubversion clearly says it should be native. If you fix it in SVN to what it should be according to https://svn.boost.org/trac/boost/wiki/BoostSubversion all becomes good.  
  
Thirdly: Regarding tools/regression/test/test-cases/general/bjam.log, I am at a loss because I could not replicate that on any of my machines here.  
  
Niall

---
