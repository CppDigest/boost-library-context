# #89 - Build failure with python extentions [Closed]

> Username: AdamMajer  
> Created at: 2019-05-29 09:20:49 UTC  
> Updated at: 2019-09-13 13:59:46 UTC  
> Closed at: 2019-09-13 13:59:46 UTC  
> Url: https://github.com/boostorg/mpi/issues/89  

After patching boost 1.70 with 3ecbf83ff33750ceffda199de97283d200357633 to get mpi python extension built, I came up upon a build error that did not happen before.  
  
```  
[  186s] error: Name clash for '<p/home/abuild/rpmbuild/BUILD/boost_1_70_0/python-stage/lib>libboost_serialization.so.1.70.0'  
[  186s] error:   
[  186s] error: Tried to build the target twice, with property sets having   
[  186s] error: these incompatible properties:  
[  186s] error:   
[  186s] error:     -  <dll-path>/usr/lib64/python2.7  
[  186s] error:     -  none  
```  
  
If I remove boost_serialization dependency from the boost_mpi target, it fails later on during the build anyway where it is complaining about boost_mpi when building with --with-graph_parallel --with-python  
  
The full build logs are here,  
  
https://build.opensuse.org/package/live_build_log/home:adamm:boost_test/boost:extra/openSUSE_Tumbleweed/x86_64  
  
There is no build issues before fixing the build with the above patch, aside that that mpi extension is not built.

---

## Comment 1

> Username: AdamMajer  
> Created at: 2019-05-29 13:30:58 UTC  
> Url: https://github.com/boostorg/mpi/issues/89#issuecomment-496937614  

If I do not specify paths in the user config and only use defaults, as  
  
```  
using python ;  
using mpi ;  
```  
I get similar error,  
  
```  
[   35s] error: Name clash for '<p/home/abuild/rpmbuild/BUILD/boost_1_70_0/python-stage/lib>libboost_serialization.so.1.70.0'  
[   35s] error:   
[   35s] error: Tried to build the target twice, with property sets having   
[   35s] error: these incompatible properties:  
[   35s] error:   
[   35s] error:     -  <dll-path>/usr/lib <dll-path>/usr/lib/python2.7/config  
[   35s] error:     -  none  
[   35s] error:   
[   35s] error: Please make sure to have consistent requirements for these   
[   35s] error: properties everywhere in your project, especially for install  
[   35s] error: targets.  
```

---

## Comment 2

> Username: aminiussi  
> Created at: 2019-06-11 17:38:54 UTC  
> Url: https://github.com/boostorg/mpi/issues/89#issuecomment-500948209  

Not sure I can easily help on that one, I am not that fluent with bjam to say the least.  
  
What is the "--python-buildid=py2.7" for ? I've never used that one

---

## Comment 3

> Username: AdamMajer  
> Created at: 2019-06-11 19:03:16 UTC  
> Url: https://github.com/boostorg/mpi/issues/89#issuecomment-500979576  

That's just so the created libraries have the python version suffix. Then it's possible to install py-3 and py-2.7 concurrently.

---

## Comment 4

> Username: AdamMajer  
> Created at: 2019-09-13 13:59:46 UTC  
> Url: https://github.com/boostorg/mpi/issues/89#issuecomment-531248382  

I'm closing this issue. It was misconfiguration on my part and not a bug in the mpi build code.
