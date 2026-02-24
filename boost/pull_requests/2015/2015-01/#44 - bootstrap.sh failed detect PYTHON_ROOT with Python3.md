# #44 bootstrap.sh failed detect PYTHON_ROOT with Python3 [Closed]

> Username: MennoTammens  
> Created at: 2015-01-20 12:42:50 UTC  
> Updated at: 2017-10-30 15:20:52 UTC  
> Closed at: 2017-10-30 15:20:52 UTC  
> Url: https://github.com/boostorg/boost/pull/44  

Bootstrap failed on the print  with Python 3.x (print statement is converted to a function in Python3)  
Adding parentheses fixes the problem and is backwards compatible with Python 2.x  
  
```  
Detecting Python version... 3.4  
Detecting Python root...   File "<string>", line 1  
    import sys; print sys.prefix  
                        ^  
SyntaxError: invalid syntax  
```  
  
Fixes:  
Ticket #6946 https://svn.boost.org/trac/boost/ticket/6946  
Ticket #5677 https://svn.boost.org/trac/boost/ticket/5677

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-26 13:55:44 UTC  
> Url: https://github.com/boostorg/boost/pull/44#issuecomment-339673969  

@danieljames this PR is no longer necessary.  It was resolved in 1.59.0 in https://github.com/boostorg/boost/commit/51e693134f5c354f2694fe89d14421c85bfacf31#diff-3af436e6760a0c5f1e3d16a3384853a8 and this PR is also targeting the wrong branch.  
  
Also, the two related Trac defects should be marked as fixed and the milestone set to 1.59.0.  I would do this myself except I cannot set a milestone before 1.61.0 in the pick list?  (This is true even for projects I am the maintainer for, so if that can be fixed that would be great...)

---

## Comment 2

> Username: danieljames  
> Created_at: 2017-10-26 14:52:54 UTC  
> Url: https://github.com/boostorg/boost/pull/44#issuecomment-339692259  

Why are you addressing these tickets to me? I'm not a Trac admin, so I'd just leave the milestone empty. I also don't know anything about python 3.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-26 15:09:56 UTC  
> Url: https://github.com/boostorg/boost/pull/44#issuecomment-339697846  

Apologies, it looked like you had done a merge here.  Why should I notify about cleaning up the pull request backlog in the superproject?  Just mail the mailing list?

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-10-26 15:23:49 UTC  
> Url: https://github.com/boostorg/boost/pull/44#issuecomment-339702607  

@grafikrobot this PR is no longer necessary - see above.  It looks like you can commit into develop.  Just trying to clean up the backlog on the superproject; check the other ones as well.

---
