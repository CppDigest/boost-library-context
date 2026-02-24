# #410 - Confusion over boost::python installation and example/tutorial instructions [Open]

> Username: JayAtlis  
> Created at: 2023-02-02 01:47:27 UTC  
> Updated at: 2023-02-02 17:10:41 UTC  
> Url: https://github.com/boostorg/python/issues/410  

Hello,  
  
I'm attempting to install boost::python on a Raspbian bullseye (32 bit) system.  First, I followed the instructions at https://www.boost.org/doc/libs/1_81_0/more/getting_started/unix-variants.html to build bjam (b2) and install the boost::python libraries and headers...  
  
```  
jay@raspberrypi:~/boost_1_81_0 $ ./bootstrap.sh --with-libraries=python --prefix=/home/jay/boost_libs  
...  
jay@raspberrypi:~/boost_1_81_0 $ ./b2 install  
```  
It completed without error.  
  
Then, I followed the instructions at https://www.boost.org/doc/libs/1_81_0/libs/python/doc/html/tutorial/tutorial/hello.html to build the example...  
  
```  
jay@raspberrypi:~/boost_1_81_0/libs/python/example/tutorial $ ~/boost_1_81_0/b2  
...found 11 targets...  
...updating 3 targets...  
gcc.link.dll hello_ext.so  
/usr/bin/ld: cannot find -lboost_python  
collect2: error: ld returned 1 exit status  
  
    "g++"    -o "hello_ext.so" -Wl,-h -Wl,hello_ext.so -shared -Wl,--start-group "hello.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lboost_python -ldl -lpthread -lutil -Wl,--end-group -fPIC -g  
  
...failed gcc.link.dll hello_ext.so...  
...skipped <p.>hello for lack of <p.>hello_ext.so...  
...failed updating 1 target...  
...skipped 2 targets...  
```  
It seems that b2 isn't correctly locating the boost_python library.  I've also tried installing the boost libraries and headers to the system (/usr/local/) and it made no difference.  
  
I can see the libraries in the filesystem:  
  
```  
jay@raspberrypi:~ $ ls ~/boost_libs/lib/  
cmake  libboost_numpy39.a  libboost_numpy39.so  libboost_numpy39.so.1.81.0  libboost_python39.a  libboost_python39.so  libboost_python39.so.1.81.0  
jay@raspberrypi:~ $ ls /usr/local/lib/  
cmake  libboost_numpy39.a  libboost_numpy39.so  libboost_numpy39.so.1.81.0  libboost_python39.a  libboost_python39.so  libboost_python39.so.1.81.0  python3.9  
```  
Please can you help me determine what's missing?  
  
-Thanks,  
-Jay

---

## Comment 1

> Username: JayAtlis  
> Created at: 2023-02-02 16:19:25 UTC  
> Updated at: 2023-02-02 16:19:48 UTC  
> Url: https://github.com/boostorg/python/issues/410#issuecomment-1414007189  

I was able to resolve the issue by creating a new symlink:  
`$ sudo ln -s /usr/local/lib/libboost_python39.so /usr/local/lib/libboost_python.so`  
  
Now I can build, but I suspect this was a hack and didn't address the root problem.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2023-02-02 16:33:57 UTC  
> Url: https://github.com/boostorg/python/issues/410#issuecomment-1414029062  

I agree. This seems to be an issue with `Boost.Build`. @pdimov , @grafikrobot , any thoughts ?

---

## Comment 3

> Username: grafikrobot  
> Created at: 2023-02-02 17:10:41 UTC  
> Url: https://github.com/boostorg/python/issues/410#issuecomment-1414083787  

It's an effect of how the BPL example Jamfiles are written. Specifically they don't have a mechanism for finding, or specifying, the BPL library. The examples default to looking for "boost_python" lib/so/dll (https://github.com/boostorg/python/blob/master/example/Jamroot#L21).
