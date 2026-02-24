# #65 Use mingw [Closed]

> Username: jschueller  
> Created at: 2017-09-28 05:28:01 UTC  
> Updated at: 2017-10-05 04:51:53 UTC  
> Closed at: 2017-09-30 13:02:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/65  

It seems some work has begun to use mingw on appveyor, this goes a little further so it compiles and builds some tests but not all succeed.

---

## Comment 1

> Username: jschueller  
> Created_at: 2017-09-30 13:02:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/65#issuecomment-333307124  

see #63

---

## Comment 2

> Username: robertramey  
> Created_at: 2017-10-05 01:00:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/65#issuecomment-334330850  

Hmmm - How come this was closed?  Should it not be merged in? Looks like it made things work.  and I had a couple of questions:  
  
..\..\..\b2 target-os=windows toolset=gcc variant=release threading=multi link=static,shared runtime-link=shared address-model=32 architecture=x86 cxxflags="-std=c++11  
  
 -O2 // why use this with varian=release  
  
-g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions --param=ssp-buffer-size=4" // what is all this stuff?

---

## Comment 3

> Username: jschueller  
> Created_at: 2017-10-05 04:51:53 UTC  
> Url: https://github.com/boostorg/serialization/pull/65#issuecomment-334358486  

It looked like #63 is duplicate.  
I just copied the standard flags when compiling mingw packages on Fedora.

---
