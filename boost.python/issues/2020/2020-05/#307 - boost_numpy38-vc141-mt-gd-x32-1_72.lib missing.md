# #307 - boost_numpy38-vc141-mt-gd-x32-1_72.lib missing [Open]

> Username: lucwens  
> Created at: 2020-05-11 16:04:21 UTC  
> Updated at: 2021-02-24 13:29:37 UTC  
> Url: https://github.com/boostorg/python/issues/307  

I get a link error using Numpy (boost version 1.72) with Visual Studio 2017 and Python 3.8.   
In my test program which includes I get a link error "boost_numpy38-vc141-mt-gd-x32-1_72.lib". I cannot find the file "boost_numpy38-vc141-mt-gd-x32-1_72.lib" anywhere, it is not created when building the binaries (bootstrap + .\b2) and it is nowhere to be found on the binary repositories at Sourceforge (https://sourceforge.net/projects/boost/files/boost-binaries/ ).  
  
Boost version 1.72  
Python 3.8  
Visual studio 2017  
Included files: boost\python\numpy.hpp  
  
LINK : fatal error LNK1104: cannot open file 'boost_numpy38-vc141-mt-gd-x32-1_72.lib'

---

## Comment 1

> Username: petulka8s  
> Created at: 2021-02-24 13:23:12 UTC  
> Url: https://github.com/boostorg/python/issues/307#issuecomment-785071297  

Hi,  
  
I am solving the same problem now.  
  
Where you able to solve this? I am quite desperate from it.  
The file boost_numpy38-vc140-mt-x64.1_72.dll is missing!

---

## Comment 2

> Username: lucwens  
> Created at: 2021-02-24 13:29:37 UTC  
> Url: https://github.com/boostorg/python/issues/307#issuecomment-785076073  

I believe I got it solved with this one: https://stackoverflow.com/questions/61609980/using-boost-numpy-with-visual-studio-2019-and-python-3-8/61898921#61898921
