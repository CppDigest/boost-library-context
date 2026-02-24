# #152 - error: Could not create directory [Closed]

> Username: pavel-machyniak  
> Created at: 2016-12-14 14:06:51 UTC  
> Updated at: 2016-12-14 17:18:21 UTC  
> Closed at: 2016-12-14 17:18:21 UTC  
> Url: https://github.com/boostorg/build/issues/152  

Environment:  
boost: 1.62  
os: Windows 10  
compiler: msvc-12  
  
On start of clean build:  
  
`error: Could not create directory '/D:/Projects/somepath/.out'`  
  
  
In `util\path.jam`, in `makedirs` at line 458 although `.out` directory is created, error is logged and build stopped.  
  
https://github.com/boostorg/build/blob/develop/src/util/path.jam#L458

---

## Comment 1

> Username: pavel-machyniak  
> Created at: 2016-12-14 16:17:22 UTC  
> Url: https://github.com/boostorg/build/issues/152#issuecomment-267077781  

When I add some debugging output to `makedirs` it works just fine.  
  
`local r = [ MAKEDIR $(native) ] ;`  
`ECHO "MAKEDIR $(native) result $(r)" ;`  
`if ! r`  
  
 At top of our `jamroot` we are gathering _svn version info_ of our project, it is started as python script and it creates the `.out` directory by itself. It is strange that later `makedirs` has issues.
