# #443 - Boost.Python doesn't build with Python3.6 on Centos7 (scl rh-python36) [Open]

> Username: olivas  
> Created at: 2019-05-25 13:45:04 UTC  
> Updated at: 2021-07-02 16:28:16 UTC  
> Url: https://github.com/boostorg/build/issues/443  

On CentOS7 with rh-python36 (scl), boost.python fails to build because the header path appears to be truncated.  
  
Here's a relevant snippet from the output:  
```  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG  -I"." -I"/opt/rh/rh-python36/root/usr/include/python3.6" -c -o "bin.v2/libs/python/build/gcc-7.3.1/release/python-3.6/threading-multi/visibility-hidden/exec.o" "libs/python/src/exec.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-7.3.1/release/python-3.6/threading-multi/visibility-hidden/exec.o...  
gcc.compile.c++ bin.v2/libs/python/build/gcc-7.3.1/release/python-3.6/threading-multi/visibility-hidden/object/function_doc_signature.o  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/converter/registrations.hpp:8,  
                 from libs/python/src/object/function_doc_signature.cpp:9:  
./boost/python/detail/wrap_python.hpp:50:11: fatal error: pyconfig.h: No such file or directory  
 # include <pyconfig.h>  
           ^~~~~~~~~~~~  
compilation terminated.  
```  
  
Apparently python36 is installed in '/opt/rh/rh-python36/root/usr/include/python3.6m' and not '/opt/rh/rh-python36/root/usr/include/python3.6'  
  
Why RH decided to append the 'm', I have no idea.  I also don't know why Boost.Build is lopping that off.  I have to imagine the fix is relatively simple though.

---

## Comment 1

> Username: mwk088  
> Created at: 2019-11-12 15:22:07 UTC  
> Url: https://github.com/boostorg/build/issues/443#issuecomment-552940190  

Any movement on this open issue?

---

## Comment 2

> Username: gvalkov  
> Created at: 2019-12-14 19:29:16 UTC  
> Url: https://github.com/boostorg/build/issues/443#issuecomment-565745388  

You can work around this by manually specifying the include path.  
  
```  
./bootstrap.sh --with-libraries=python --with-python=/opt/rh/rh-python36/root/bin/python3.6m   
```   
  
Now edit `project-config.jam` and change the `using python ...` line to:  
  
```  
    using python : 3.6 : "/usr" : "/opt/rh/rh-python36/root/usr/include/python3.6m" ;  
```  
  
Hope this helps.

---

## Comment 3

> Username: qhaas  
> Created at: 2020-06-24 13:50:22 UTC  
> Updated at: 2021-07-02 16:28:16 UTC  
> Url: https://github.com/boostorg/build/issues/443#issuecomment-648832850  

I'm seeing this issue in EL7.8, python3, and boost 1.69.  [This resolved it](https://github.com/boostorg/build/issues/289#issuecomment-406925734), but not an option for those without admin.  
  
UPDATE:  Still an Issue in EL 7.9 as of 7/2021 in boost 1.72.0

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 17:21:59 UTC  
> Url: https://github.com/boostorg/build/issues/443#issuecomment-850868254  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
