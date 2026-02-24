# #96 - boost::python v1.63 mangles py2 for py3 build with numpy [Closed]

> Username: abemammen  
> Created at: 2017-01-03 16:23:58 UTC  
> Updated at: 2017-01-04 02:19:20 UTC  
> Closed at: 2017-01-04 02:19:20 UTC  
> Url: https://github.com/boostorg/python/issues/96  

with the integration of numpy into v1.63, it appears there is mangling of python2 libs for python3 environments.  
Run time error:  
Symbol not found: _PyClass_Type  
Referenced from: /usr/local/opt/boost-python/lib/libboost_python.dylib  
  
built on mac with brew:  
brew install boost  
brew install boost-python --with-python3

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-01-03 22:26:12 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270243170  

I just tried  
```  
$ brew install boost-python --with-python3   
```  
which resulted in a command  
```  
./bootstrap.sh --prefix=/usr/local/Cellar/boost-python/1.63.0 --libdir=/usr/local/Cellar/boost-python/1.63.0/lib --with-libraries=python --with-python=python --with-python-root=/System/Library/Frameworks/Python.framework/Versions/2.7  
...  
```  
I don't know the `brew` tool and how it translates its own command-line arguments into sub-commands, but the above looks clearly wrong. I suggest you report this to the `brew` project instead.

---

## Comment 2

> Username: abemammen  
> Created at: 2017-01-03 22:31:07 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270244326  

Hi Stefan,  
I noticed that too but its followed by a second ./bootstrap.sh command  
which uses 3.4.  
  
brew install boost-python --with-python3  
  
==> *Using the sandbox*  
  
==> *Downloading  
https://downloads.sourceforge.net/project/boost/boost/1.63.0/boost_1_63_0.tar.bz2  
<https://downloads.sourceforge.net/project/boost/boost/1.63.0/boost_1_63_0.tar.bz2>*  
  
Already downloaded:  
/Users/amammen/Library/Caches/Homebrew/boost-python-1.63.0.tar.bz2  
  
==> *./bootstrap.sh --prefix=/usr/local/Cellar/boost-python/1.63.0  
--libdir=/usr/local/Cellar/boost-python/1.63.0/lib --with-libraries=python  
--with-python=python  
--with-python-root=/System/Library/Frameworks/Python.framework/Versions/2.7*  
  
==> *./b2 --build-dir=build-python --stagedir=stage-python python=2.7  
--prefix=/usr/local/Cellar/boost-python/1.63.0  
--libdir=/usr/local/Cellar/boost-python/1.63.0/lib -d2 -j8 --layout=tagged  
--user-config=user-config.jam threading=multi,single link=sh*  
  
==> *./bootstrap.sh --prefix=/usr/local/Cellar/boost-python/1.63.0  
--libdir=/usr/local/Cellar/boost-python/1.63.0/lib --with-libraries=python  
--with-python=python3  
--with-python-root=/Library/Frameworks/Python.framework/Versions/3.4*  
  
==> *./b2 --build-dir=build-python3 --stagedir=stage-python3 python=3.4  
--prefix=/usr/local/Cellar/boost-python/1.63.0  
--libdir=/usr/local/Cellar/boost-python/1.63.0/lib -d2 -j8 --layout=tagged  
--user-config=user-config.jam threading=multi,single link=*  
  
🍺  /usr/local/Cellar/boost-python/1.63.0: 466 files, 29.8M, built in 2  
minutes 50 seconds  
  
On Tue, Jan 3, 2017 at 2:26 PM, Stefan Seefeld <notifications@github.com>  
wrote:  
  
> I just tried  
>  
> $ brew install boost-python --with-python3  
>  
> which resulted in a command  
>  
> ./bootstrap.sh --prefix=/usr/local/Cellar/boost-python/1.63.0 --libdir=/usr/local/Cellar/boost-python/1.63.0/lib --with-libraries=python --with-python=python --with-python-root=/System/Library/Frameworks/Python.framework/Versions/2.7  
> ...  
>  
> I don't know the brew tool and how it translates its own command-line  
> arguments into sub-commands, but the above looks clearly wrong. I suggest  
> you report this to the brew project instead.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/python/issues/96#issuecomment-270243170>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ANBK3JTYQn8FS9SttlxVASYT5k9DzRjFks5rOssEgaJpZM4LZxoG>  
> .  
>  
  
  
  
--   
____________________  
Abe Mammen  
mammen.abe@gmail.com  
+1 925-577-5201(m)

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-01-03 22:50:35 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270248388  

Oh, now I see what you are reporting: while the boost_python library is built twice, once as `libboost_python` and once as `libboost_python3`, there is only `libboost_numpy`, i.e. presumably the python3-version of it should be named `libboost_numpy3`, rather than having the same name as the python-version. Correct ?

---

## Comment 4

> Username: abemammen  
> Created at: 2017-01-03 22:59:32 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270250167  

Not quite.  
  
It does build a libboost_numpy3. I have linked with libboost_python3 and  
libboost_numpy3 and get that error.  
  
Let me recreate the python 3.4 environment as I moved it away from a few  
days ago using python 2.7 and numpy via boost python v1.63 and that all  
worked fine (eventually) but I did that only because the 3.4 environment  
wasn't working  with:  
Run time error:  
Symbol not found: _PyClass_Type  
Referenced from: /usr/local/opt/boost-python/lib/libboost_python.dylib  
​)  
  
​NOTE: the reference to libboost_python.dylib not libboost_python3.dylib  
  
  
On Tue, Jan 3, 2017 at 2:50 PM, Stefan Seefeld <notifications@github.com>  
wrote:  
  
> Oh, now I see what you are reporting: while the boost_python library is  
> built twice, once as libboost_python and once as libboost_python3, there  
> is only libboost_numpy, i.e. presumably the python3-version of it should  
> be named libboost_numpy3, rather than having the same name as the  
> python-version. Correct ?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/python/issues/96#issuecomment-270248388>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ANBK3A7fYZs8z5ASJrNShpRIqsbLTxXaks5rOtC8gaJpZM4LZxoG>  
> .  
>  
  
  
  
--   
____________________  
Abe Mammen  
mammen.abe@gmail.com  
+1 925-577-5201(m)

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2017-01-03 23:04:47 UTC  
> Updated at: 2017-01-03 23:05:26 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270251138  

OK, then I'm not quite sure what you are saying:  
After the build, you have libboost_python.dylib and libboost_python3.dylib, as well as libboost_numpy.dylib and libboost_numpy3.dylib. But rather than having libboost_python3.dylib and libboost_numpy3.dylib referencing symbols in libpython3.x.dylib, they are referencing symbols in libpython2.x.dylib ? Is that what you are saying ?  
  
In other words: could you please give a specific test case that should pass but fails ?

---

## Comment 6

> Username: abemammen  
> Created at: 2017-01-03 23:06:16 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270251401  

It appears that way.  
  
On Tue, Jan 3, 2017 at 3:04 PM, Stefan Seefeld <notifications@github.com>  
wrote:  
  
> OK, then I'm not quite sure what you are saying:  
> After the build, you have libboost_python.dylib and  
> libboost_python3.dylib, as well as libboost_numpy.dylib and  
> libboost_numpy3.dylib. But rather than having libboost_python3.dylib and  
> libboost_numpy3.dylib referencing symbols in libpython3.x.dylib, they are  
> referencing symbols in libpython2.x.dylib ? Is that what you are saying ?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/python/issues/96#issuecomment-270251138>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ANBK3LCruYUuELTB7ZBPgVIJsfku7_giks5rOtQPgaJpZM4LZxoG>  
> .  
>  
  
  
  
--   
____________________  
Abe Mammen  
mammen.abe@gmail.com  
+1 925-577-5201(m)

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2017-01-03 23:25:23 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270254731  

OK, I believe I see the problem. Can you try out this patch:  
  
```  
diff --git a/build/Jamfile b/build/Jamfile  
index 313fdab..235c519 100644  
--- a/build/Jamfile  
+++ b/build/Jamfile  
@@ -140,7 +140,7 @@ rule lib_boost_numpy ( is-py3 ? )  
             [ cond [ python.numpy ] : <library>/python//python_for_extensions ]  
             [ unless [ python.numpy ] : <build>no ]  
            <include>$(numpy-include)  
-           <library>boost_python  
+           [ cond $(is-py3) : <library>boost_python3 : <library>boost_python ]  
             <python-debugging>on:<define>BOOST_DEBUG_PYTHON  
             [ cond $(is-py3) : <python>$(py3-version) ]  
   
```  
  
(to the `libs/python/build/Jamfile` file in the boost source tree) ?

---

## Comment 8

> Username: abemammen  
> Created at: 2017-01-03 23:44:21 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270257693  

OK. I'll give that a try.  
  
On Tue, Jan 3, 2017 at 3:25 PM, Stefan Seefeld <notifications@github.com>  
wrote:  
  
> OK, I believe I see the problem. Can you try out this patch:  
>  
> diff --git a/build/Jamfile b/build/Jamfile  
> index 313fdab..235c519 100644  
> --- a/build/Jamfile  
> +++ b/build/Jamfile  
> @@ -140,7 +140,7 @@ rule lib_boost_numpy ( is-py3 ? )  
>              [ cond [ python.numpy ] : <library>/python//python_for_extensions ]  
>              [ unless [ python.numpy ] : <build>no ]  
>             <include>$(numpy-include)  
> -           <library>boost_python  
> +           [ cond $(is-py3) : <library>boost_python3 : <library>boost_python ]  
>              <python-debugging>on:<define>BOOST_DEBUG_PYTHON  
>              [ cond $(is-py3) : <python>$(py3-version) ]  
>  
>  
> (to the libs/python/build/Jamfile file in the boost source tree) ?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/python/issues/96#issuecomment-270254731>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ANBK3MYIPy7qKjNd0FU0JtFU01hdFjMfks5rOtjjgaJpZM4LZxoG>  
> .  
>  
  
  
  
--   
____________________  
Abe Mammen  
mammen.abe@gmail.com  
+1 925-577-5201(m)

---

## Comment 9

> Username: abemammen  
> Created at: 2017-01-04 01:59:39 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270277292  

That worked. Can this change be rolled into the repo? I would like to get it from <head> and build with brew. I have a linux version to configure and build as well so would prefer to get this change directly from source.

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2017-01-04 02:05:51 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270278102  

Great, thanks for testing ! Yes, I will roll it into the develop branch (and eventually merge into master). Can you build from the github repo ?  
  
Thanks for raising this and helping me understand !

---

## Comment 11

> Username: abemammen  
> Created at: 2017-01-04 02:10:12 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270278648  

I didn't build from the github repo but instead just grabbed the downloaded version that Brew had initiated via:  
https://downloads.sourceforge.net/project/boost/boost/1.63.0/boost_1_63_0.tar.bz2.  
But I did that for the Linux version so I can do the same - I just need to know the name of the develop branch.

---

## Comment 12

> Username: stefanseefeld  
> Created at: 2017-01-04 02:19:20 UTC  
> Url: https://github.com/boostorg/python/issues/96#issuecomment-270279731  

I just pushed the fix to the `develop` branch in https://github.com/boostorg/python/, so you should be able to build from there.  
Thanks again !
