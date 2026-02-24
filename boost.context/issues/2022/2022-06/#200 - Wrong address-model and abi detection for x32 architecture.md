# #200 - Wrong address-model and abi detection for x32 architecture? [Closed]

> Username: bigon  
> Created at: 2022-06-08 11:32:21 UTC  
> Updated at: 2023-08-14 10:31:33 UTC  
> Closed at: 2022-09-18 13:54:35 UTC  
> Url: https://github.com/boostorg/context/issues/200  

Hello,  
  
In https://github.com/boostorg/context/blob/develop/build/Jamfile.v2 I see that the `<address-model>` for the x32 architecture is set to 64bits  
  
But the point of x32 is to use 32bits pointers, isn't it?  
  
If you look on debian [build logs](https://buildd.debian.org/status/fetch.php?pkg=boost1.74&arch=x32&ver=1.74.0-16&stamp=1654149541&raw=0) you see that it detects the architecture as 32bits  
  
The debian package had to disable the build of the context library (and others that use asm) because of this wrong detection  
  
I tested, and changing the  `<address-model>` in  https://github.com/boostorg/context/blob/develop/build/Jamfile.v2 to 32 for x32 AND setting the abi to x32 during the build allows me to build the context library properly (I didn't try more than building it). Both changes are needed otherwise it tries to build using i386 asm code and the build fails  
  
So I guess the detection of the abi is also boggus?

---

## Comment 1

> Username: olk  
> Created at: 2022-09-18 13:55:01 UTC  
> Updated at: 2022-09-18 13:55:34 UTC  
> Url: https://github.com/boostorg/context/issues/200#issuecomment-1250313992  

changed as adviced - ty

---

## Comment 2

> Username: SoapGentoo  
> Created at: 2023-08-14 10:31:32 UTC  
> Url: https://github.com/boostorg/context/issues/200#issuecomment-1677077756  

Hi @olk  
on a related note, Gentoo has been carrying a [patch](https://raw.githubusercontent.com/gentoo/gentoo/master/dev-libs/boost/files/boost-1.79.0-context-x32.patch) for x32 for ages, do you think this could be upstreamed?
