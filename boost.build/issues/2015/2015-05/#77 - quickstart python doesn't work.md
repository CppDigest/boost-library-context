# #77 - quickstart python doesn't work [Closed]

> Username: ghost  
> Created at: 2015-05-04 10:26:03 UTC  
> Updated at: 2015-05-05 10:30:00 UTC  
> Closed at: 2015-05-04 14:49:19 UTC  
> Url: https://github.com/boostorg/build/issues/77  

Hi   
I use booost_1_58_0 with gcc (Ubuntu 4.8.2-19ubuntu1) 4.8.2, I used the easy way to install it   
  
``` bash  
./bootstrap.sh   
./b2  
sudo ./b2 install  
```  
  
Shared libraries are in /usr/lib//usr/lib/x86_64-linux-gnu/, and header are in /usr/include/boost/ ,  
the example from  http://www.boost.org/doc/libs/1_58_0/more/getting_started/unix-variants.html#link-your-program-to-a-boost-library work well with   
  
``` bash  
c++  example.cpp -o example  -lboost_regex  
```  
  
Now, my objective is to use python. so I tried with libs/python/example/tutorial and it worked after adding boost-build.jam  
the problems start with quickstart, when I compiled I got http://paste.debian.net/171254/ , you'll find 2 errors messages :   
  
```  
*** Error in `bin/gcc-4.8/debug/embedding': double free or corruption (!prev): 0x000000000149f560 ***  
testing.capture-output bin/test_embed.test/gcc-4.8/debug/test_embed.run  
```  
  
```  
...failed testing.capture-output bin/test_embed.test/gcc-4.8/debug/test_embed.run...  
...failed updating 1 target...  
...skipped 1 target...  
...updated 15 targets..  
```  
  
I seems that tests was passed successfully, but when I used  python test_extending.py  
http://paste.debian.net/171251/  
Second question : am I obliged to keep boost_1_58_0 directory in on my file system ?    
I found /usr/local/share/boost-build/ and /usr/share/boost-build/  but I don't know if it can replace boost_1_58_0

---

## Comment 1

> Username: vprus  
> Created at: 2015-05-04 14:29:40 UTC  
> Url: https://github.com/boostorg/build/issues/77#issuecomment-98726851  

Thanks for your report - it does indeed seem worrysome.  
  
However, double free looks like Boost.Python bug, and not being able to import the extension module is probably PYTHONPATH not set - which Boost.Python docs should explain. So it's seems like reporting this to Boost.Python team will be more appropriate?

---

## Comment 2

> Username: ghost  
> Created at: 2015-05-04 14:49:19 UTC  
> Url: https://github.com/boostorg/build/issues/77#issuecomment-98737475  

ok, I'll do :)

---

## Comment 3

> Username: ghost  
> Created at: 2015-05-05 10:16:18 UTC  
> Url: https://github.com/boostorg/build/issues/77#issuecomment-99019734  

I can't post on https://github.com/boostorg/python   
issue section doesn't exist

---

## Comment 4

> Username: jhunold  
> Created at: 2015-05-05 10:30:00 UTC  
> Url: https://github.com/boostorg/build/issues/77#issuecomment-99024874  

Boost.Python uses the official bugtracker. See https://svn.boost.org/trac/boost/query?status=!closed&component=Python for the current issues and report there.
