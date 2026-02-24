# #197 - boost 1.86 beta and cobalt shared library [Closed]

> Username: bramasub1  
> Created at: 2024-08-12 21:14:22 UTC  
> Updated at: 2024-08-14 20:07:45 UTC  
> Closed at: 2024-08-14 20:07:45 UTC  
> Url: https://github.com/boostorg/cobalt/issues/197  

Is the cobalt shared library not built anymore ?  I am using   
  
./b2 --prefix=/opt/boost_1_86 --reconfigure --with-python toolset=gcc cxxflags="-std=c++20 -O3"  
./b2 install --prefix=/opt/boost_1_86_install  
  
and I don't see it in the output.  My compiler is gcc12.3

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2024-08-13 23:09:48 UTC  
> Updated at: 2024-08-13 23:10:11 UTC  
> Url: https://github.com/boostorg/cobalt/issues/197#issuecomment-2287362074  

Add --with-cobalt to your  build command

---

## Comment 2

> Username: bramasub1  
> Created at: 2024-08-14 20:07:45 UTC  
> Url: https://github.com/boostorg/cobalt/issues/197#issuecomment-2289777497  

That worked.
