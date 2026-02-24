# #65 Complete jamfile fixes for python-tag. [Merged]

> Username: xnox  
> Created at: 2018-07-04 16:27:23 UTC  
> Updated at: 2018-07-30 13:45:20 UTC  
> Merged at: 2018-07-30 13:09:47 UTC  
> Closed at: 2018-07-30 13:09:47 UTC  
> Url: https://github.com/boostorg/mpi/pull/65  

Previously python-tags were specified, but actually ineffective as  
python-tag is not an available rule. Copy&Paste python-tag rule from  
libs/python/Jamfile.  
  
This now correctly creates `libboost_mpi_python27.so` to match `libboost_python27.so` & `libboost_numpy27.so`.

---

## Comment 1

> Username: aminiussi  
> Created_at: 2018-07-30 09:12:06 UTC  
> Url: https://github.com/boostorg/mpi/pull/65#issuecomment-408798848  

The patch fails to compile on my linux cluster with messages like:  
```  
ntel-linux.compile.c++ ../../../bin.v2/libs/mpi/build/intel-linux/debug/threading-multi/python/collectives.o  
In file included from ../../../boost/python/detail/prefix.hpp(13),  
                 from ../../../boost/python/args.hpp(8),  
                 from ../../../boost/python.hpp(11),  
                 from ../../../libs/mpi/src/python/collectives.cpp(14):  
../../../boost/python/detail/wrap_python.hpp(50): catastrophic error: cannot open source file "pyconfig.h"  
  # include <pyconfig.h>  
                        ^  
  
compilation aborted for ../../../libs/mpi/src/python/collectives.cpp (code 4)  
```  
  
The compilation command is:  
  
```  
"icpc" -c -xc++ -fPIC -m64 -pthread -O0 -g -w1 -inline-level=0 -Xlinker --enable-new-dtags -Xlinker -rpath -Xlinker /trinity/shared/apps/170135/x86_64/intel-2018_u3/compilers_and_libraries_2018.3.222/linux/mpi/intel64/lib/release_mt -Xlinker -rpath -Xlinker /trinity/shared/apps/170135/x86_64/intel-2018_u3/compilers_and_libraries_2018.3.222/linux/mpi/intel64/lib -Xlinker -rpath -Xlinker /opt/intel/mpi-rt/2017.0.0/intel64/lib/release_mt -Xlinker -rpath -Xlinker /opt/intel/mpi-rt/2017.0.0/intel64/lib -DBOOST_ALL_NO_LIB=1 -DBOOST_MPI_DYN_LINK=1 -DBOOST_MPI_PYTHON_DYN_LINK=1 -DBOOST_PYTHON_DYN_LINK=1 -I"../../.." -I"/trinity/shared/OCA/softs/anaconda3/include/python3.6" -I"/trinity/shared/apps/170135/x86_64/intel-2018_u3/compilers_and_libraries_2018.3.222/linux/mpi/intel64/include"   -c -o "../../../bin.v2/libs/mpi/build/intel-linux/debug/threading-multi/python/collectives.o" "../../../libs/mpi/src/python/collectives.cpp  
```  
I suspect the problem is that the include path   
**/trinity/shared/OCA/softs/anaconda3/include/python3.6** should actually be **/trinity/shared/OCA/softs/anaconda3/include/python3.6m**.  
Do you know if there is a way to specify that modification ? I suppose the **m** suffix common enough and that there is a "usual" way of fixing this ?  
  
Thanks

---

## Comment 2

> Username: aminiussi  
> Created_at: 2018-07-30 13:10:24 UTC  
> Url: https://github.com/boostorg/mpi/pull/65#issuecomment-408857525  

It's merge. Thanks and sorry for the delay.

---

## Comment 3

> Username: xnox  
> Created_at: 2018-07-30 13:12:38 UTC  
> Url: https://github.com/boostorg/mpi/pull/65#issuecomment-408858176  

@aminiussi the python includes are not created here, but come from the boost build macros further up.  
  
It might be that the detection or configuration of anaconda is borked. What is the output for you of:  
  
```  
$ python3-config --includes  
-I/usr/include/python3.6m -I/usr/include/python3.6m  
```  
  
Does anaconda3 ship python3-config or something similar?  
  
Or for example what is the output of  
```  
$ python3 -c 'import sysconfig; import pprint; pprint.pprint(sysconfig.get_paths())'  
{'data': '/usr',  
 'include': '/usr/include/python3.6m',  
 'platinclude': '/usr/include/python3.6m',  
 'platlib': '/usr/lib/python3.6/site-packages',  
 'platstdlib': '/usr/lib/python3.6',  
 'purelib': '/usr/lib/python3.6/site-packages',  
 'scripts': '/usr/bin',  
 'stdlib': '/usr/lib/python3.6'}  
```  
make sure e.g. the include path that is configured there, actually exists and is the right one. Then probably you would need to open a bug against boost build project, to ensure that your python install is correctly detected. Please include all the configure flags / config that you pass to your build.

---

## Comment 4

> Username: aminiussi  
> Created_at: 2018-07-30 13:25:53 UTC  
> Url: https://github.com/boostorg/mpi/pull/65#issuecomment-408862082  

There is a python3-config shipped with anaconda:  
```  
$ which python3-config  
/trinity/shared/OCA/softs/anaconda3/bin/python3-config  
$  python3-config --includes  
-I/trinity/shared/OCA/softs/anaconda3/include/python3.6m -I/trinity/shared/OCA/softs/anaconda3/include/python3.6m  
```  
And the directory does exist .  
  
I'll proceed with the boost build project then.  
  
Thanks

---

## Comment 5

> Username: aminiussi  
> Created_at: 2018-07-30 13:45:20 UTC  
> Url: https://github.com/boostorg/mpi/pull/65#issuecomment-408868510  

Actually, I got the lib building by explicitly specifying the include directory:  
```  
# Python configuration  
import python ;  
if ! [ python.configured ]  
{  
    using python : 3.6 : /trinity/shared/OCA/softs/anaconda3 : /trinity/shared/OCA/softs/anaconda\  
3/include/python3.6m ;  
}  
  
```

---
