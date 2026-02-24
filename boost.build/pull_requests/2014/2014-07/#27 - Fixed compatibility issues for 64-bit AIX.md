# #27 Fixed compatibility issues for 64-bit AIX [Closed]

> Username: ismirlian  
> Created at: 2014-07-31 21:51:43 UTC  
> Updated at: 2021-10-02 22:35:50 UTC  
> Closed at: 2014-08-14 12:53:37 UTC  
> Url: https://github.com/boostorg/build/pull/27  

I added bbigtoc flag because I was getting some TOC overflow errors in the some of the submodules, including math, and I wanted to fix the problem in a more permanent way that did not involve the user adding that flag to their user-config.jam. The tests might run slower, but I don't think this is really an issue.  
  
The archiver that boost chooses for AIX to use is not its native archiver, it is the linux one. This in itself is not a problem except for the fact that there is a typo in the last line where there is an extra space that causes a syntax error.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-08-14 12:53:37 UTC  
> Url: https://github.com/boostorg/build/pull/27#issuecomment-52179033  

Thanks for your fix. It has been merged.

---
