# #663 - Building Boost-Build in an armv7 Docker container result in a not working binary [Closed]

> Username: Abdulla060  
> Created at: 2020-10-13 22:29:47 UTC  
> Updated at: 2021-09-27 16:47:31 UTC  
> Closed at: 2021-09-27 16:47:31 UTC  
> Url: https://github.com/boostorg/build/issues/663  

steps to reproduce:  
simply build this Dockerfile  
```  
FROM arm32v7/ubuntu:latest  
  
WORKDIR /root  
  
RUN apt-get update  
RUN apt-get install -y build-essential wget  
  
RUN wget https://dl.bintray.com/boostorg/release/1.74.0/source/boost_1_74_0.tar.gz  
RUN tar -xzf boost_1_74_0.tar.gz  
  
WORKDIR /root/boost_1_74_0/tools/build  
RUN ./bootstrap.sh  
RUN ./b2  
  
#for testing  
CMD bash  
```  
  
this will result in the following error:  
```  
/root/boost_1_74_0/tools/build/src/build/type.jam:158: in type.validate from module type  
error: Unknown target type EXE  
(builtin):-1: in sequence.transform from module sequence  
/root/boost_1_74_0/tools/build/src/build/generators.jam:239: in generator.__init__ from module object(installed-exe-generator)@4  
/root/boost_1_74_0/tools/build/src/tools/stage.jam:568: in class@installed-exe-generator.__init__ from module object(installed-exe-generator)@4  
/root/boost_1_74_0/tools/build/src/kernel/class.jam:90: in new from module class  
/root/boost_1_74_0/tools/build/src/tools/stage.jam:596: in load from module stage  
/root/boost_1_74_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
/root/boost_1_74_0/tools/build/src/tools/builtin.jam:27: in load from module builtin  
/root/boost_1_74_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
/root/boost_1_74_0/tools/build/src/build-system.jam:12: in load from module build-system  
/root/boost_1_74_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
/root/boost_1_74_0/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
/root/boost_1_74_0/tools/build/boost-build.jam:8: in module scope from module  
```  
I've tested on Ubuntu with `multiarch/qemu-user-static` and in Windows 10 with Docker desktop and got the exact same problem. however, if I use my Raspberry Pi 4 to build the same Dockerfile, then everything works without a problem.

---

## Comment 1

> Username: alvarolb  
> Created at: 2020-11-01 10:25:31 UTC  
> Url: https://github.com/boostorg/build/issues/663#issuecomment-720065783  

Same error here!  
  
```  
#9 292.7 /boost_1_74_0/tools/build/src/build/type.jam:158: in type.validate from module type  
#9 292.7 error: Unknown target type EXE  
#9 292.7 (builtin):-1: in sequence.transform from module sequence  
#9 292.7 /boost_1_74_0/tools/build/src/build/generators.jam:239: in generator.__init__ from module object(installed-exe-generator)@4  
#9 292.7 /boost_1_74_0/tools/build/src/tools/stage.jam:568: in class@installed-exe-generator.__init__ from module object(installed-exe-generator)@4  
#9 292.7 /boost_1_74_0/tools/build/src/kernel/class.jam:90: in new from module class  
#9 292.7 /boost_1_74_0/tools/build/src/tools/stage.jam:596: in load from module stage  
#9 292.7 /boost_1_74_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
#9 292.7 /boost_1_74_0/tools/build/src/tools/builtin.jam:27: in load from module builtin  
#9 292.7 /boost_1_74_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
#9 292.7 /boost_1_74_0/tools/build/src/build-system.jam:12: in load from module build-system  
#9 292.7 /boost_1_74_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
#9 292.7 /boost_1_74_0/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
#9 292.7 /boost_1_74_0/boost-build.jam:17: in module scope from module  
#9 292.7  
```

---

## Comment 2

> Username: rmorales87atx  
> Created at: 2021-04-23 22:57:19 UTC  
> Url: https://github.com/boostorg/build/issues/663#issuecomment-825982835  

Having a similar problem, building inside a chroot based on Raspberry Pi OS (which is officially still 32-bit armv7 right now).  
  
```  
root@robert-virtualbox:/opt/boost_1_76_0# ./bootstrap.sh   
Building B2 engine..  
  
###  
###  
### Using 'gcc' toolset.  
###  
###  
  
g++ (Raspbian 8.3.0-6+rpi1) 8.3.0  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
  
###  
###  
  
> g++ -x c++ -std=c++11 -O2 -s -DNDEBUG builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp execcmd.cpp execnt.cpp execunix.cpp filesys.cpp filent.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam_strings.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp object.cpp option.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp startup.cpp subst.cpp sysinfo.cpp timestamp.cpp variable.cpp w32_getreg.cpp modules/order.cpp modules/path.cpp modules/property-set.cpp modules/regex.cpp modules/sequence.cpp modules/set.cpp -o b2  
> cp b2 bjam  
tools/build/src/engine/b2  
Unicode/ICU support for Boost.Regex?... not found.  
Generating B2 configuration in project-config.jam for gcc...  
  
Bootstrapping is done. To build, run:  
  
    ./b2  
      
To generate header files, run:  
  
    ./b2 headers  
  
The configuration generated uses gcc to build by default. If that is  
unintended either use the --with-toolset option or adjust configuration, by  
editing 'project-config.jam'.  
  
Further information:  
  
   - Command line help:  
     ./b2 --help  
       
   - Getting started guide:   
     http://www.boost.org/more/getting_started/unix-variants.html  
       
   - B2 documentation:  
     http://www.boost.org/build/  
  
root@robert-virtualbox:/opt/boost_1_76_0# ./b2  
/opt/boost_1_76_0/tools/build/src/build/type.jam:158: in type.validate from module type  
error: Unknown target type EXE  
(builtin):-1: in sequence.transform from module sequence  
/opt/boost_1_76_0/tools/build/src/build/generators.jam:239: in generator.__init__ from module object(installed-exe-generator)@4  
/opt/boost_1_76_0/tools/build/src/tools/stage.jam:568: in class@installed-exe-generator.__init__ from module object(installed-exe-generator)@4  
/opt/boost_1_76_0/tools/build/src/kernel/class.jam:90: in new from module class  
/opt/boost_1_76_0/tools/build/src/tools/stage.jam:596: in load from module stage  
/opt/boost_1_76_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
/opt/boost_1_76_0/tools/build/src/tools/builtin.jam:27: in load from module builtin  
/opt/boost_1_76_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
/opt/boost_1_76_0/tools/build/src/build-system.jam:12: in load from module build-system  
/opt/boost_1_76_0/tools/build/src/kernel/modules.jam:295: in import from module modules  
/opt/boost_1_76_0/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
/opt/boost_1_76_0/boost-build.jam:17: in module scope from module  
```

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:19 UTC  
> Url: https://github.com/boostorg/build/issues/663#issuecomment-850859577  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
