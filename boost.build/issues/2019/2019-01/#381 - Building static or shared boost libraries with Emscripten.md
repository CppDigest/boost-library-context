# #381 - Building static or shared boost libraries with Emscripten [Closed]

> Username: akontsevich  
> Created at: 2019-01-05 10:14:59 UTC  
> Updated at: 2023-04-25 15:35:40 UTC  
> Closed at: 2023-04-25 15:35:39 UTC  
> Url: https://github.com/boostorg/build/issues/381  

Trying to build boost libraries with following commands  
  
    cd boost_1_68_0  
    ./bootstrap.sh  
    ./b2 toolset=emscripten link=static variant=release threading=single runtime-link=static system random date_time  
However it produces `.bc` files to me, not `.a` for `static` build flags or `.so` for `shared` build flags. Then I have problem linking that libraries into my WebAssebly project. How to produce `.a` or `.so` files here? Thanks!

---

## Comment 1

> Username: akontsevich  
> Created at: 2019-01-06 09:35:37 UTC  
> Updated at: 2019-01-06 10:45:45 UTC  
> Url: https://github.com/boostorg/build/issues/381#issuecomment-451728048  

Solved for now by running  
```  
# emar q library_name.a *.bc  
```  
for every `.bc` file in a directory.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:20 UTC  
> Url: https://github.com/boostorg/build/issues/381#issuecomment-859203478  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.

---

## Comment 3

> Username: mpconte  
> Created at: 2022-05-24 21:03:57 UTC  
> Url: https://github.com/boostorg/build/issues/381#issuecomment-1136430263  

Thanks for your contribution @akontsevich, but I still encounter issues with building my CMake project. When I attempt to `make` my project I get the following error:  
  
`em++: error: /usr/local/boost_emscripten/lib/libboost_system.a: Unknown format, not a static library!`  
  
Have you encountered this?

---

## Comment 4

> Username: David-Herzog-Vexcel  
> Created at: 2023-04-25 15:15:12 UTC  
> Url: https://github.com/boostorg/build/issues/381#issuecomment-1521970905  

I am stuck on this problem, please help:  
https://github.com/emscripten-core/emscripten/issues/17187

---

## Comment 5

> Username: grisumbras  
> Created at: 2023-04-25 15:24:20 UTC  
> Url: https://github.com/boostorg/build/issues/381#issuecomment-1521994814  

OP in that issue had  
```  
E:\emsdk\python\3.9.2-nuget_64bit\python.exe: can't open file 'E:\boost\boost_1_78_0\emcc.py': [Errno 2] No such file or directory  
```  
This line implies that `emscripten` toolset was not configured properly. So you have to be more specific,provide the build output, add your Boost/b2 config and so on.

---

## Comment 6

> Username: grafikrobot  
> Created at: 2023-04-25 15:35:39 UTC  
> Url: https://github.com/boostorg/build/issues/381#issuecomment-1522011590  

https://github.com/bfgroup/b2/issues/170  
https://github.com/bfgroup/b2/pull/111
