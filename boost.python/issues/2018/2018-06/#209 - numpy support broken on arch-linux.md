# #209 - numpy support broken on arch-linux [Closed]

> Username: luca-penasa  
> Created at: 2018-06-05 08:20:40 UTC  
> Updated at: 2018-07-11 23:44:08 UTC  
> Closed at: 2018-06-08 12:06:42 UTC  
> Url: https://github.com/boostorg/python/issues/209  

This seems to be a bug affecting several distros. I testes on Manjaro (fresh and updated live) and Ubuntu 18.04 (docker).  
  
Here is the code for reproducing the bug:  
https://github.com/luca-penasa/boost-numpy-minimal-bug  
  
it compiles a python module using boost-python and boost-python-numpy  
the line of code   
```cpp  
np::initialize();  
```  
causes an error when trying to import the module in python (here from ipython but it is the same from pure python):  
```  
Python 3.6.5 (default, May 11 2018, 04:00:52)   
Type 'copyright', 'credits' or 'license' for more information  
IPython 6.3.1 -- An enhanced Interactive Python. Type '?' for help.  
  
In [1]: import hello  
---------------------------------------------------------------------------  
RuntimeError                              Traceback (most recent call last)  
RuntimeError: FATAL: module compiled as little endian, but detected different endianness at runtime  
---------------------------------------------------------------------------  
ImportError                               Traceback (most recent call last)  
ImportError: numpy.core.umath failed to import  
---------------------------------------------------------------------------  
SystemError                               Traceback (most recent call last)  
<ipython-input-1-f81fb083bdeb> in <module>()  
----> 1 import hello  
  
SystemError: initialization of hello raised unreported exception  
  
In [2]:   
  
```  
  
Could anybody have a look at this? this is making a lot of my code useless!

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2018-06-05 13:13:54 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-394704016  

As the error indicates, it looks like the boost_numpy3 module was compiled for a different architecture (endianness). Please report this to the packager, i.e. the distro you are using.

---

## Comment 2

> Username: luca-penasa  
> Created at: 2018-06-05 13:31:03 UTC  
> Updated at: 2018-06-05 13:31:35 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-394709658  

thank you for the reply @stefanseefeld   
That would also mean that both in the case of ubuntu and arch they are compiling boost with the wrong endianess... It sounds unlikely to me. what do you think? I cannot be sure the problem lies within boost-python-numpy but it seemed to me ther more likely.  ... I've been also trying recompiling it several times with no success.   
  
On what distro are you running it?  
does the sample code compile (and run) for you?  
  
I am hitting the same problem both on my workstation and on the laptop...  
  
thanks

---

## Comment 3

> Username: luca-penasa  
> Created at: 2018-06-05 13:35:50 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-394711294  

btw  
i am on manjaro (arch) witht the following packages:  
python 3.6.5  
numpy 1.14.3  
boost 1.67.0  
  
I've been tring several versions of boost and numpy (trunk) with no success... any idea on how I could debug it?

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2018-06-05 13:37:22 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-394711804  

I have been using Boost.Python (including NumPy) on different distros. Notably, we are running unit tests in a trusty container (see https://travis-ci.org/boostorg/python for details) without any issues.  
  
But as I said, the Boost.Python project doesn't itself produce the binaries that are shipped in Linux distributions, so it doesn't control how they get built. I haven't heard of the problem you are reporting before, so I'm not sure where to look for the bug. But the nature of the problem suggests a build (or even runtime !) configuration issue. In either case, it's outside my control.  
  
You mention you have been building Boost.Python yourself, and are observing the problem. How exactly are you building it ? Can you provide the build logs ? And what Boost.Python version are we talking about ?

---

## Comment 5

> Username: luca-penasa  
> Created at: 2018-06-05 13:55:15 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-394718139  

Thanks btw for your time. I can understand it does not look a problem related to boost python (which btw works fine).   
  
I've been building several versions of boost (comprising ::python and :.numpy) through the package builder of arch (makepkg) which uses a PKGBUILD file describing the compilation process. (You might be right if this problem comes out during compile the PKGBUILD might just replicate the error).  
  
I got this issue since a recent update of the system so it might be related to new versions of some packages (python? numpy?). unfortunately it is not so easy to go back to previous versions.  
  
Trusty uses also "old" packages that might be the reason for which it works fine on those platforms, as I said I got this error since a couple of days after an update. the same on my laptop where I am running arch too.  
  
I can provide the commands needed to replicate the error in docker with ubuntu if needed.  
  
I will try to recompile boost python by itself and see what happens.   
  
Is there anything I could do to better constraint the origin of this issue? any idea?

---

## Comment 6

> Username: luca-penasa  
> Created at: 2018-06-05 14:05:15 UTC  
> Updated at: 2018-06-05 14:06:13 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-394721776  

here are the commands to reproduce this issue from within a docker container. If any of you could try to replicate this would be of great help to me, so I can understand if this is just my problem or not  
  
run a fresh container:  
```bash  
docker run -ti ubuntu /bin/bash  
```  
  
and then from within it:  
```bash  
apt-get update  
apt-get -y upgrade  
apt-get -y install build-essential git cmake libboost-all-dev python3  
git clone https://github.com/luca-penasa/boost-numpy-minimal-bug.git bnmb.git  
cd bnmb.git/  
mkdir build  
cd build  
cmake ..  
make  
python3 -c "import hello"  
```  
  
In this case the error is slightly different and is:  
```  
ImportError: numpy.core.multiarray failed to import  
ImportError: numpy.core.umath failed to import  
Traceback (most recent call last):  
  File "<string>", line 1, in <module>  
SystemError: initialization of hello raised unreported exception  
root@3e3639f9d914:/bnmb.git/build#   
```

---

## Comment 7

> Username: luca-penasa  
> Created at: 2018-06-05 14:41:44 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-394735556  

I confirm that compiling boost-python with faber works as expected and all test pass. Thus this problem likely raises from somewhere else.  
  
I'll try to use this version of boost python instead than the official one but this raises the point that there might be some problem with the version distributed within boost   
  
e.g. Arch compile boost/python from the package provided here:  
https://sourceforge.net/projects/boost/  
  
I don't know how the confluence of boost/python to boost works and how to get which version of boost/python is used there...  
  
  
If I can help in any way please don't hesitate  
many thanks.  
luca

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2018-06-05 16:02:55 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-394766034  

Good to see that you got things working ! I'm not sure what you are referring to as "distributed within boost". Boost (the org) typically only distributes source releases (at least on Linux), so what you may be thinking of as a "release" is actually produced by individual distributions (debian, ubuntu, fedora, etc.). This is why I suggested to submit the issue to them.

---

## Comment 9

> Username: Scimmia22  
> Created at: 2018-06-08 15:04:28 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-395789744  

Note that "FATAL: module compiled as little endian, but detected different endianness at runtime" is happening on an x86 system, so it IS little endian. There's a problem with the detection.

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2018-06-08 15:11:33 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-395791835  

I strongly suggest that you build and test Boost.Python using the standard procedures (`faber` for stand-alone builds and `b2` for an integrated build). It seems from a previous comment that in this context all tests pass, i.e. the issue being reported here isn't seen.  
If that's the case, you need to report this downstream, with whatever distro provides the binary packages that exhibit this behaviour.  
I unfortunately don't have the bandwidth to support issues related to specific binary packages, if they don't also show up as genuine Boost.Python bugs.

---

## Comment 11

> Username: luca-penasa  
> Created at: 2018-06-11 09:54:35 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-396188183  

BTW I've reported to arch. Seems that at the end it is an arch-related build problem.  
https://bugs.archlinux.org/task/58881  
on ubuntu the problem was a missing package...   
many thanks

---

## Comment 12

> Username: urbainvaes  
> Created at: 2018-06-24 15:13:19 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-399763890  

Same problem here! Is there a fix for arch, apart from downgrading boost?

---

## Comment 13

> Username: stefanseefeld  
> Created at: 2018-06-24 15:24:24 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-399764644  

@urbainvaes , I suggest you follow the above link to the arch issue tracker and ask there. This is not a Boost.Python issue.

---

## Comment 14

> Username: jwakely  
> Created at: 2018-06-29 09:51:41 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-401307469  

@stefanseefeld please could you take a look at the Fedora packaging, because we use `b2` and I don't think we do anything to affect the endianness and this has been [reported to Fedora](https://bugzilla.redhat.com/show_bug.cgi?id=1596468).

---

## Comment 15

> Username: stefanseefeld  
> Created at: 2018-07-11 23:44:08 UTC  
> Url: https://github.com/boostorg/python/issues/209#issuecomment-404344222  

https://github.com/boostorg/python/pull/218 is now merged into master, and thus will be part of `1.68`.
