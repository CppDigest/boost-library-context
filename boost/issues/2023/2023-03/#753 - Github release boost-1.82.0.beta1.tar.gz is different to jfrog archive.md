# #753 - Github release boost-1.82.0.beta1.tar.gz is different to jfrog archive [Closed]

> Username: userdocs  
> Created at: 2023-03-16 17:04:40 UTC  
> Updated at: 2023-03-16 19:54:01 UTC  
> Closed at: 2023-03-16 19:54:01 UTC  
> Url: https://github.com/boostorg/boost/issues/753  

When i take these archive URLS  
  
Release assets:   
  
https://github.com/boostorg/boost/releases/download/boost-1.81.0/boost-1.81.0.tar.gz  
https://github.com/boostorg/boost/releases/download/boost-1.82.0.beta1/boost-1.82.0.beta1.tar.gz  
  
https://boostorg.jfrog.io/artifactory/main/beta/1.82.0.beta1/source/boost_1_82_0_b1.tar.gz  
  
The Github releases appear to be processed differently and lacks the required `boost` folder.  
  
[Github release asset](https://github.com/boostorg/boost/releases/download/boost-1.82.0.beta1/boost-1.82.0.beta1.tar.gz)  
  
![2](https://user-images.githubusercontent.com/16525024/225695843-bf9f9f63-1bb4-46c7-8d4e-349bac554960.png)  
  
[Jfrog release asset](https://boostorg.jfrog.io/artifactory/main/beta/1.82.0.beta1/source/boost_1_82_0_b1.tar.gz)  
  
![1](https://user-images.githubusercontent.com/16525024/225695835-8a264467-2aa9-4e6e-a57d-8e3c0e80ac98.png)  
  
Can you clarify if these Github assets are releases in the same sense the Jfrog archive is, as currently they appear to packaged differently and lack some processing and file inclusion.  
  
In this state they a cannot be used in an equivalent fashion to compile against.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-03-16 19:11:35 UTC  
> Url: https://github.com/boostorg/boost/issues/753#issuecomment-1472602242  

These are different release archives, intended to be used with CMake, as explained [here](https://github.com/boostorg/cmake/blob/develop/README.md).

---

## Comment 2

> Username: userdocs  
> Created at: 2023-03-16 19:19:35 UTC  
> Url: https://github.com/boostorg/boost/issues/753#issuecomment-1472612134  

Oh ok, thanks.  
  
When building another app using cmake i just point to the `boost` dir and it works so if i want that i need the jfrog releases?  
  
If want to build b2 with cmake, i need this release?

---

## Comment 3

> Username: pdimov  
> Created at: 2023-03-16 19:27:22 UTC  
> Url: https://github.com/boostorg/boost/issues/753#issuecomment-1472622832  

Yes.
