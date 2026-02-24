# #163 - Boost 1.84.0 cobalt fails to build with gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0 [Closed]

> Username: yangdianxp  
> Created at: 2024-03-20 09:58:19 UTC  
> Updated at: 2024-03-26 01:55:58 UTC  
> Closed at: 2024-03-26 01:55:58 UTC  
> Url: https://github.com/boostorg/cobalt/issues/163  

My system is  
Ubuntu 22.04.3 LTS  
My compiler is  
gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0  
  
I executed the following instructions to compile boost  
  
```sh  
tar -xvf boost_1_84_0.tar.bz2  
cd boost_1_84_0  
./bootstrap.sh --prefix=/usr/local  
./  
b2  
```  
[describe.txt](https://github.com/boostorg/cobalt/files/14664526/describe.txt)

---

## Comment 1

> Username: Lyulf  
> Created at: 2024-03-25 16:04:09 UTC  
> Url: https://github.com/boostorg/cobalt/issues/163#issuecomment-2018358009  

I currently don't have access to a gcc compiler on hand to test below flags but try this:  
```  
./bootstrap.sh --prefix=/usr/local --with-libraries=cobalt,system  
./b2 cxxstd=20  
```

---

## Comment 2

> Username: yangdianxp  
> Created at: 2024-03-26 01:55:58 UTC  
> Url: https://github.com/boostorg/cobalt/issues/163#issuecomment-2019246453  

Thank you, the cobalt library has been generated.
