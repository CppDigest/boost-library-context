# #418 - b2 gives ERROR: rule "Copyright" unknown in module "xml". [Closed]

> Username: yoonghm  
> Created at: 2019-03-28 23:58:35 UTC  
> Updated at: 2019-03-29 00:36:12 UTC  
> Closed at: 2019-03-29 00:36:12 UTC  
> Url: https://github.com/boostorg/build/issues/418  

Hi,  
  
  I am using boost 1.67.0 on Ubuntu.  I let the compilation run over the night.  I had forgotten to direct outputs and errors to a file. I did `b2` again.  I have these information:  
```  
/home/s/src/boost_1_67_0/tools/build/src/tools/types/xml.jam:12: in load  
ERROR: rule "Copyright" unknown in module "xml".  
/home/s/src/boost_1_67_0/tools/build/src/kernel/modules.jam:295: in import  
/home/s/src/boost_1_67_0/tools/build/src/tools/types/register.jam:36: in load  
/home/s/src/boost_1_67_0/tools/build/src/kernel/modules.jam:295: in import  
/home/s/src/boost_1_67_0/tools/build/src/tools/stage.jam:18: in load  
/home/s/src/boost_1_67_0/tools/build/src/kernel/modules.jam:295: in import  
/home/s/src/boost_1_67_0/tools/build/src/tools/builtin.jam:27: in load  
/home/s/src/boost_1_67_0/tools/build/src/kernel/modules.jam:295: in import  
/home/s/src/boost_1_67_0/tools/build/src/build-system.jam:12: in load  
/home/s/src/boost_1_67_0/tools/build/src/kernel/modules.jam:295: in import  
/home/s/src/boost_1_67_0/tools/build/src/kernel/bootstrap.jam:139: in boost-build  
/home/s/src/boost_1_67_0/boost-build.jam:17: in module scope  
```  
  
My `prefix` is `/home/s`.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-03-29 00:01:23 UTC  
> Url: https://github.com/boostorg/build/issues/418#issuecomment-477814350  

AMDG  
  
You're using an out-dated b2.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: yoonghm  
> Created at: 2019-03-29 00:05:03 UTC  
> Url: https://github.com/boostorg/build/issues/418#issuecomment-477815057  

How do I get an updated b2? The b2 is from `https://dl.bintray.com/boostorg/release/1.67.0/source/boost_1_67_0.tar.bz2`.

---

## Comment 3

> Username: swatanabe  
> Created at: 2019-03-29 00:14:27 UTC  
> Url: https://github.com/boostorg/build/issues/418#issuecomment-477816903  

AMDG  
  
Are you certain that you are using the b2 from 1.67  
and not an (older) system installed b2?  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: yoonghm  
> Created at: 2019-03-29 00:36:12 UTC  
> Url: https://github.com/boostorg/build/issues/418#issuecomment-477820895  

You are right, there is another `/usr/bin/b2`. I did not follow the Getting Started Guide exactly.  Let me try again using boost 1.69.
