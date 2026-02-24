# #311 - ERROR: rule "errors.user-error" unknown in module "lib-generator". [Closed]

> Username: stefanseefeld  
> Created at: 2018-05-20 19:22:02 UTC  
> Updated at: 2018-05-23 01:26:42 UTC  
> Closed at: 2018-05-23 01:26:42 UTC  
> Url: https://github.com/boostorg/build/issues/311  

I'm working on a custom module (`~/clblas.jam`), which I have introduced an error into.  
However, instead of reporting that error properly, `b2` seems to trip over an internal error preventing the proper error handling from happening. Here is the stack trace  
```  
dev@ccca9ca106a8:~/stefan/projects/boost/libs/numeric/ublas/test$ ../../../../b2 --reconfigure -d+2  
/home/dev/clblas.jam:24: Unescaped special character in argument ;#  
/home/dev/stefan/projects/boost/tools/build/src/tools/generators/lib-generator.jam:85: in lib  
ERROR: rule "errors.user-error" unknown in module "lib-generator".  
/home/dev/clblas.jam:24: in load  
/home/dev/stefan/projects/boost/tools/build/src/kernel/modules.jam:295: in import  
/home/dev/stefan/projects/boost/tools/build/src/build/toolset.jam:42: in toolset.using  
/home/dev/stefan/projects/boost/tools/build/src/build/project.jam:1052: in using  
/home/dev/user-config.jam:2: in modules.load  
/home/dev/stefan/projects/boost/tools/build/src/build-system.jam:255: in load-config  
/home/dev/stefan/projects/boost/tools/build/src/build-system.jam:453: in load-configuration-files  
/home/dev/stefan/projects/boost/tools/build/src/build-system.jam:607: in load  
/home/dev/stefan/projects/boost/tools/build/src/kernel/modules.jam:295: in import  
/home/dev/stefan/projects/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build  
/home/dev/stefan/projects/boost/boost-build.jam:17: in module scope  
```

---

## Comment 1

> Username: vprus  
> Created at: 2018-05-20 19:25:55 UTC  
> Url: https://github.com/boostorg/build/issues/311#issuecomment-390505152  

Stefan,  
  
Would it be possible to provide a minimal self-contained example?

---

## Comment 2

> Username: swatanabe  
> Created at: 2018-05-20 19:40:40 UTC  
> Url: https://github.com/boostorg/build/issues/311#issuecomment-390506087  

AMDG  
  
On 05/20/2018 01:25 PM, Vladimir Prus wrote:  
> Would it be possible to provide a minimal self-contained example?  
>   
  
  The problem is pretty obvious.  It's just missing an  
import in tools/generators/lib-generator.jam before  
the call to errors.user-error (two instances).  
  
In Christ,  
Steven Watanabe

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2018-05-20 19:41:11 UTC  
> Url: https://github.com/boostorg/build/issues/311#issuecomment-390506113  

put this into `~/bug.jam`:  
```  
lib opencl ;# : : <name>OpenCL <search>/usr/lib/x86_64-linux-gnu ;  
```  
and this into `~/user-config.jam`:  
```  
using bug ;  
```  
then invoke `b2` somewhere...
