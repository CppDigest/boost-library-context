# #1 - Library requirements errors. [Closed]

> Username: grafikrobot  
> Created at: 2016-06-17 18:04:47 UTC  
> Updated at: 2016-10-30 00:12:11 UTC  
> Closed at: 2016-10-30 00:12:11 UTC  
> Url: https://github.com/boostorg/qvm/issues/1  

====== BEGIN OUTPUT ======  
libs/qvm: error: file not found; Did not find a Boost Build file in the [project-root]/build directory. The library needs to provide a Boost Build project that the user, and the top level Boost project, can use to build the library if it has sources to build.  
libs/qvm: error: file not found; Did not find [project-root]/index.html file. The file is required for all libraries. Redirection to HTML documentation.  
  
EXIT STATUS: 1  
====== END OUTPUT ======

---

## Comment 1

> Username: zajo  
> Created at: 2016-06-17 19:18:07 UTC  
> Url: https://github.com/boostorg/qvm/issues/1#issuecomment-226857958  

Thanks -- I've added index.html. The QVM library doesn't have any cpp files to build, do I still need to add a qvm/build/Jamfile? What do I put into it?

---

## Comment 2

> Username: grafikrobot  
> Created at: 2016-06-17 20:11:42 UTC  
> Url: https://github.com/boostorg/qvm/issues/1#issuecomment-226869968  

Strange.. That looks like a bug in the requirements testing script. I'll look into that.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2016-06-17 21:15:14 UTC  
> Url: https://github.com/boostorg/qvm/issues/1#issuecomment-226883391  

Actually I just realized the problem.. QVM has a 'build' directory but it doesn't have a Boost Build file in it. You have a VS solution in there. I assume you are building something with that. So it would make sense to make that build-able with b2?

---

## Comment 4

> Username: zajo  
> Created at: 2016-06-17 21:34:29 UTC  
> Url: https://github.com/boostorg/qvm/issues/1#issuecomment-226887093  

I'll just rename the build directory to something else. Let me know if that's a problem.

---

## Comment 5

> Username: grafikrobot  
> Created at: 2016-06-17 21:37:30 UTC  
> Url: https://github.com/boostorg/qvm/issues/1#issuecomment-226887639  

Shouldn't be a problem to rename it.
