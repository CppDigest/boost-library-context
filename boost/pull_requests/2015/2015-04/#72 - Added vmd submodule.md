# #72 Added vmd submodule [Merged]

> Username: eldiener  
> Created at: 2015-04-01 04:09:55 UTC  
> Updated at: 2015-04-03 15:41:48 UTC  
> Merged at: 2015-04-03 08:28:54 UTC  
> Closed at: 2015-04-03 08:28:54 UTC  
> Url: https://github.com/boostorg/boost/pull/72  



---

## Comment 1

> Username: danieljames  
> Created_at: 2015-04-03 08:28:50 UTC  
> Url: https://github.com/boostorg/boost/pull/72#issuecomment-89218944  

It will also need to added to status/Jamfile.v2 to start testing, but it's probably best to wait until the release is out, as they're sorting out some issue with the build system.

---

## Comment 2

> Username: danieljames  
> Created_at: 2015-04-03 08:47:10 UTC  
> Url: https://github.com/boostorg/boost/pull/72#issuecomment-89225333  

I changed it to the develop branch: 19f4bfb613f2e31f5576d337f70ceac7f265fe92

---

## Comment 3

> Username: eldiener  
> Created_at: 2015-04-03 15:14:50 UTC  
> Url: https://github.com/boostorg/boost/pull/72#issuecomment-89316423  

What is status/Jamfile.v2 about ? I do have a jamfile.v2 in the test directory.

---

## Comment 4

> Username: swatanabe  
> Created_at: 2015-04-03 15:41:48 UTC  
> Url: https://github.com/boostorg/boost/pull/72#issuecomment-89331592  

AMDG  
  
On 04/03/2015 09:14 AM, Edward Diener wrote:  
  
> What is status/Jamfile.v2 about ? I do have a jamfile.v2 in the test directory.  
  
status/Jamfile.v2 is the global list of tests used  
by the regression testing system.  It should probably  
be updated to use a glob, so we don't have to go  
through this every time a new library is added.  
  
In Christ,  
Steven Watanabe

---
