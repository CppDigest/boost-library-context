# #4 Confusing documentation on efficient ADD/MUL invocations. [Closed]

> Username: Y-Less  
> Created at: 2014-11-13 15:38:39 UTC  
> Updated at: 2014-12-05 05:18:48 UTC  
> Closed at: 2014-12-05 05:18:48 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/4  

The documentation for ADD, ADD_D, MUL, and MUL_D all have a confusing paragraph about how best to use them, implying both that "x" should be the smallest and largest parameter for best operation.  This was reported previously here:  
  
https://svn.boost.org/trac/boost/ticket/5580  
  
I came across the same issue today, and after some digging I think that the statement that "x" should be the greater parameter is correct (based on BOOST_PP_WHILE decrementing the second parameter, and thus having that as the smaller one will result in less iterations).  I've fixed the four instances of this error in the documentation that I know of.  
  
Where the changed "(c)" symbol in those commits came from, I don't know (I only just noticed them), but they seem to be changed back to the correct thing.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-11-18 04:27:20 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/4#issuecomment-63421146  

This PR is fine except that it's against master and should be against develop.  Please close this PR and resubmit against develop.

---

## Comment 2

> Username: eldiener  
> Created_at: 2014-12-05 05:18:48 UTC  
> Url: https://github.com/boostorg/preprocessor/pull/4#issuecomment-65748694  

I have updated the documentation on the 'develop' branch accordingly and will update it to 'master' before the next release.

---
