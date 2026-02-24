# #7 Changes to FindOpenCL.cmake module [Closed]

> Username: d-meiser  
> Created at: 2013-04-17 20:13:18 UTC  
> Updated at: 2014-07-31 02:36:33 UTC  
> Closed at: 2013-04-24 00:23:37 UTC  
> Url: https://github.com/boostorg/compute/pull/7  

Hey Kyle,  
  
I made a few changes to the FindOpenCL module that make this work better on my machine. I think this may be generally useful. The strategy is to search in the following locations  
- A user specified OpenCL_ROOT_DIR (e.g. cmake -DOpenCL_ROOT_DIR=/opt/AMDAPP source_dir)  
- If none is given the $CUDA_TOOLKIT_ROOT_DIR is searched  
- If that can't be found search in $AMDAPPSDKROOT  
  In addition the normal system path is search (e.g. /usr/lib64 etc.)  
  
Cheers,  
Dominic

---

## Comment 1

> Username: kylelutz  
> Created_at: 2013-04-17 23:38:54 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16542274  

I was actually thinking about pulling in the FindOpenCL file from VexCL (https://github.com/ddemidov/vexcl/blob/master/cmake/FindOpenCL.cmake) as it is more widely used and better tested (and many other OpenCL projects use the same code). Could you try that code and check if it works for your system(s)?  
  
@ddemidov Are there any problems/licensing concerns with copying VexCL's FindOpenCL?

---

## Comment 2

> Username: d-meiser  
> Created_at: 2013-04-17 23:48:39 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16542611  

Ok, I'll give VexCL's FindOpenCL a shot.  
Cheers,  
Dominic  
  
On Wed, Apr 17, 2013 at 5:38 PM, Kyle Lutz notifications@github.com wrote:  
  
> I was actually thinking about pulling in the FindOpenCL file from VexCL (  
> https://github.com/ddemidov/vexcl/blob/master/cmake/FindOpenCL.cmake) as  
> it is more widely used and better tested (and many other OpenCL projects  
> use the same code). Could you try that code and check if it works for your  
> system(s)?  
>   
> @ddemidov https://github.com/ddemidov Are there any problems/licensing  
> concerns with copying VexCL's FindOpenCL?  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/kylelutz/compute/pull/7#issuecomment-16542274  
> .

---

## Comment 3

> Username: ddemidov  
> Created_at: 2013-04-18 02:59:10 UTC  
> Updated_at: 2013-04-18 02:59:56 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16551809  

Hi Kyle, you are welcome to borrow whatever you need from VexCL :).  
  
The code is MIT licensed, so I don't see any problems with that. The FindOpenCL.cmake was actually submitted by @raedwulf. It does work both in Linux and Windows (and, since, @raedwulf's development system is MacOs X, it should work there as well).  
  
The only problem I have with 64bit windows/AMD SDK is, inspite of [this line](https://github.com/ddemidov/vexcl/blob/master/cmake/FindOpenCL.cmake#L34), 32bit library gets picked. I have to change the path to point to 64bit manually.  
  
Btw, since you apparently are working for Kitware, do you know if there are any plans for inclusion of some FindOpenCL.cmake into the set of official cmake modules?

---

## Comment 4

> Username: kylelutz  
> Created_at: 2013-04-18 03:11:32 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16552533  

Excellent! Thanks!  
  
And I'll talk to some people tomorrow about getting FindOpenCL integrated into cmake's standard modules.

---

## Comment 5

> Username: ddemidov  
> Created_at: 2013-04-18 03:23:25 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16553168  

I've found the origin of vexcl's FindOpenCL.cmake: its http://gitorious.org/findopencl and it appears to be in public domain.

---

## Comment 6

> Username: d-meiser  
> Created_at: 2013-04-18 16:01:50 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16585675  

Hey Kyle,  
I replaced FindOpenCL.cmake by the module in vexcl. This includes ddemidov's changes from last night. I works on a windows box and a linux box that I have access to.  
Cheers,  
Dominic

---

## Comment 7

> Username: kylelutz  
> Created_at: 2013-04-19 00:06:10 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16620664  

Excellent! Glad to hear it works with VexCL's FindOpenCL.   
  
However, I'd like to go the other way with this. Instead of adapting the FindOpenCL file to Boost.Compute, I'd rather adapt Boost.Compute to use the new FindOpenCL (and ideally it would be a verbatim copy from VexCL). That way we can propose the new FindOpenCL module for inclusion in cmake itself and Boost.Compute, VexCL, and other OpenCL projects can use it. It shouldn't be too much work, mostly just replacing the cmake variable names in Boost.Compute's CMakeLists files. Sorry for not being clear on that before.  
  
Also, with your commit messages, it's conventional to have a "subject" as the first line, followed by a blank line, followed by a description of the commit. This way it is easier to use with "git log --oneline" and "git reflog". You can look at the other commit messages as a guide.  
  
Let me know if you need any help.  
  
Thanks,  
Kyle

---

## Comment 8

> Username: d-meiser  
> Created_at: 2013-04-22 13:48:40 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16786780  

Hey Kyle,  
  
Sounds great. I'll probably have time to work on this tonight. Thanks for  
the pointer about the commit messages. I'm new to git and github and my  
workflow probably sucks. If you have any other pointers or suggestions  
please let me know.  
  
Btw have you noticed that the tests that a really long time to build with  
clang, many times longer than gcc? Probably a performance issue on the  
clang side.  
  
Cheers,  
Dominic  
  
On Thu, Apr 18, 2013 at 5:06 PM, Kyle Lutz notifications@github.com wrote:  
  
> Excellent! Glad to hear it works with VexCL's FindOpenCL.  
>   
> However, I'd like to go the other way with this. Instead of adapting the  
> FindOpenCL file to Boost.Compute, I'd rather adapt Boost.Compute to use the  
> new FindOpenCL (and ideally it would be a verbatim copy from VexCL). That  
> way we can propose the new FindOpenCL module for inclusion in cmake itself  
> and Boost.Compute, VexCL, and other OpenCL projects can use it. It  
> shouldn't be too much work, mostly just replacing the cmake variable names  
> in Boost.Compute's CMakeLists files. Sorry for not being clear on that  
> before.  
>   
> Also, with your commit messages, it's conventional to have a "subject" as  
> the first line, followed by a blank line, followed by a description of the  
> commit. This way it is easier to use with "git log --oneline" and "git  
> reflog". You can look at the other commit messages as a guide.  
>   
> Let me know if you need any help.  
>   
> Thanks,  
> Kyle  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/kylelutz/compute/pull/7#issuecomment-16620664  
> .

---

## Comment 9

> Username: ddemidov  
> Created_at: 2013-04-23 06:45:26 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16842024  

For me the complete building time for tests, examples and benchmarks is 6m14.040s (gcc-4.6.3) vs 6m20.570s (clang 3.2).

---

## Comment 10

> Username: d-meiser  
> Created_at: 2013-04-23 15:46:04 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16866661  

That's interesting. Perhaps I ended up with a debug build of clang by  
accident.  
  
On Tue, Apr 23, 2013 at 12:45 AM, Denis Demidov notifications@github.comwrote:  
  
> For me the complete building time for tests, examples and benchmarks is  
> 6m14.040s (gcc-4.6.3) vs 6m20.570s (clang 3.2).  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/kylelutz/compute/pull/7#issuecomment-16842024  
> .

---

## Comment 11

> Username: kylelutz  
> Created_at: 2013-04-24 00:23:37 UTC  
> Url: https://github.com/boostorg/compute/pull/7#issuecomment-16899065  

Looks great, I've cherry-picked it into my repo here: 2a93124ef51d8c4dd4482e0321a449aa3e0c3881.  
  
Thanks!

---
