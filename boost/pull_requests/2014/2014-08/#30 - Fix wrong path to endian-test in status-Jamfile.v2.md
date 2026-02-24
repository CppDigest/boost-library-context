# #30 Fix wrong path to endian/test in status/Jamfile.v2 [Merged]

> Username: Flast  
> Created at: 2014-08-15 16:54:22 UTC  
> Updated at: 2014-08-15 20:44:40 UTC  
> Merged at: 2014-08-15 20:44:04 UTC  
> Closed at: 2014-08-15 20:44:04 UTC  
> Url: https://github.com/boostorg/boost/pull/30  

You can get following error message.  
  
```  
[vagrant@master ~/boost/status]$ b2  
/usr/home/vagrant/boost/tools/build/src/build/project.jam:262: in find-jamfile from module project  
error: Unable to load Jamfile.  
error: Could not find a Jamfile in directory '../libs/endian'.  
error: Attempted to find it with pattern '[Bb]uild.jam [Jj]amfile.v2 [Jj]amfile [Jj]amfile.jam'.  
error: Please consult the documentation at 'http://www.boost.org'.  
/usr/home/vagrant/boost/tools/build/src/build/project.jam:280: in load-jamfile from module project  
/usr/home/vagrant/boost/tools/build/src/build/project.jam:64: in load from module project  
/usr/home/vagrant/boost/tools/build/src/build/project.jam:89: in load-used-projects from module project  
/usr/home/vagrant/boost/tools/build/src/build/project.jam:75: in load from module project  
/usr/home/vagrant/boost/tools/build/src/build/project.jam:145: in project.find from module project  
/usr/home/vagrant/boost/tools/build/src/build-system.jam:535: in load from module build-system  
/usr/home/vagrant/boost/tools/build/src/kernel/modules.jam:289: in import from module modules  
/usr/home/vagrant/boost/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
/usr/home/vagrant/boost/boost-build.jam:17: in module scope from module  
```

---

## Comment 1

> Username: Beman  
> Created_at: 2014-08-15 20:44:40 UTC  
> Url: https://github.com/boostorg/boost/pull/30#issuecomment-52355562  

Thanks!  
  
--Beman

---
