# #479 - missing package metadata in boost_1_75_0.tar.gz [Closed]

> Username: VirenderNayal  
> Created at: 2021-02-19 14:57:14 UTC  
> Updated at: 2021-06-08 14:48:56 UTC  
> Closed at: 2021-06-08 14:48:56 UTC  
> Url: https://github.com/boostorg/boost/issues/479  

I was trying to use boost libraries,   
I downloaded the boost_1_75_0.tar.gz package from the official website of boost.org.  
  
When i tried to install the package it gives error:  
  
![image](https://user-images.githubusercontent.com/72212823/108520362-9906a880-72f0-11eb-8419-4960d0c352f9.png)  
  
I am using Endeavour OS (Arch Based).

---

## Comment 1

> Username: flyingcakes85  
> Created at: 2021-06-08 09:20:22 UTC  
> Url: https://github.com/boostorg/boost/issues/479#issuecomment-856607816  

That link downloads the source of boost. To install it using `pacman` you need to write a PKGBUILD.  
  
A lot more easier method should be to install it from package repos.  
  
For runtime, install `boost-libs`  
  
```  
sudo pacman -S boost-libs  
```  
  
For development, install `boost`  
  
```  
sudo pacman -S boost  
```
