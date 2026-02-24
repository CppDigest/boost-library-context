# #163 Develop [Closed]

> Username: BeErikk  
> Created at: 2017-09-22 07:30:42 UTC  
> Updated at: 2017-09-23 18:37:09 UTC  
> Closed at: 2017-09-22 10:52:21 UTC  
> Url: https://github.com/boostorg/boost/pull/163  

ignore 64-bit stage directory - add to .gitignore  
  
`index 231a4508f..91ae80b13 100644  
--- a/.gitignore  
+++ b/.gitignore  
@@ -7,7 +7,7 @@  
 /boost  
 /dist  
 /project-config.jam*  
-/stage  
+/stage*  
 /user-config.jam  
 /.settings/  
 /.project`  
  
edit   
hmm lol - "BeErikk  wants to merge 10,000 commits into master from develop"  
No I don't, just the above patch

---

## Comment 1

> Username: danieljames  
> Created_at: 2017-09-22 10:28:10 UTC  
> Url: https://github.com/boostorg/boost/pull/163#issuecomment-331411352  

You branched from develop, so you need to make to pull request against develop.

---

## Comment 2

> Username: BeErikk  
> Created_at: 2017-09-22 10:51:58 UTC  
> Url: https://github.com/boostorg/boost/pull/163#issuecomment-331415748  

@danieljames  
  
Thanks. How to fix it now?

---

## Comment 3

> Username: danieljames  
> Created_at: 2017-09-22 11:00:36 UTC  
> Url: https://github.com/boostorg/boost/pull/163#issuecomment-331417327  

We use trac for bug reports/feature requests:  
  
https://svn.boost.org/trac10/newticket  
  
Or you can open a pull request by editing the file here:  
  
https://github.com/boostorg/boost/blob/develop/.gitignore  
  
Just click on the pencil icon at the top right of the code.  
  
I'm not keen on the change though, it seems like the pattern could accidentally match other things. Perhaps we could use a more specific pattern. What's the name of directory you want to ignore?

---

## Comment 4

> Username: BeErikk  
> Created_at: 2017-09-22 20:47:43 UTC  
> Updated_at: 2017-09-22 20:48:28 UTC  
> Url: https://github.com/boostorg/boost/pull/163#issuecomment-331556732  

Hello Daniel  
The directory is `stage_x64`. I see now that this directory was created by an external building script for b2 to build 64-bit with VS2017, which at least was a bit of a challenge. So, your right and I'm very sorry for the fuzz. close this

---

## Comment 5

> Username: danieljames  
> Created_at: 2017-09-23 18:37:09 UTC  
> Url: https://github.com/boostorg/boost/pull/163#issuecomment-331661669  

I'll add an ignore for `stage_x64`, that's no problem.

---
