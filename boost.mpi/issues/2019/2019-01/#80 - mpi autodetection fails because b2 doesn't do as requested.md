# #80 - mpi autodetection fails because b2 doesn't do as requested [Closed]

> Username: iljah  
> Created at: 2019-01-07 10:48:56 UTC  
> Updated at: 2019-10-22 15:27:26 UTC  
> Closed at: 2019-10-22 15:27:26 UTC  
> Url: https://github.com/boostorg/mpi/issues/80  

Trying to install boost mpi on a system where mpi compiler executable is called CC. For some reason adding `using mpi : CC ;` to tools/build/src/user-config.jam doesn't work and b2 says that mpi autodetection failed. If I instead add `using mpi : CC : <define>DAMNITB2JUSTUSECC ;` everything works. Naturally the added definition doesn't affect anything as far as C(++) code goes so why is it needed? Shouldn't b2 in both cases just use CC when compiling mpi code? Seems like a bug...

---

## Comment 1

> Username: aminiussi  
> Created at: 2019-01-07 11:08:45 UTC  
> Url: https://github.com/boostorg/mpi/issues/80#issuecomment-451901047  

I suspect a documentation bug. We don't mention what to do when there is not wrapper.  
  
-  `using mpi : CC ;` tell the build system to treat CC as a compiler wrapper and try to extract the compilation options, header, libs etc. in order to buld and MPI code with the C++ compiler (selected by the tool set).  It is used when the mpi wrapper does not have a "comon" name or is not in the default path but otherwise support usual detection options such as `-show`  
- `using mpi : CC : <define>DAMNITB2JUSTUSECC ;` tell the build system that we do not rely on usual detection options (such as `-show`)  but explicitly provide compilation and link options. In that case 'CC' is not really used (not sure if it's optional), but we (well, you) still need to inhibit the other detection methods, which is achieved with dummy define option.

---

## Comment 2

> Username: aminiussi  
> Created at: 2019-01-07 11:12:06 UTC  
> Url: https://github.com/boostorg/mpi/issues/80#issuecomment-451901883  

We need to at least mention that case in the documentation.  Not sure it's easy to fix through b2 (which wasn't very MPI friendly last time I checked). Probably better (less worse) to fix it through cmake at some point.  
  
What platform is this ?  
Thanks

---

## Comment 3

> Username: iljah  
> Created at: 2019-01-07 11:27:20 UTC  
> Updated at: 2019-01-07 11:28:45 UTC  
> Url: https://github.com/boostorg/mpi/issues/80#issuecomment-451905686  

Thanks. I've also been adding `using gcc : 7.1.0 : CC ;` because that's also the regular c++ compiler, which probably explains why adding above define makes things work. The name CC is used on Cray machines for c++ compiler (+mpi), but this is an issue on any platform where a particular executable must be used when compiling (without any extra flags), e.g. also https://www.nas.nasa.gov/hecc/resources/pleiades.html  
  
In my experience autodetections usually don't work in supercomputer environment and I wouldn't be surprised if cmake also won't. On one system all executables had to be run through queuing system which made it painful to install packages that use gnu automake/autoconf...

---

## Comment 4

> Username: iljah  
> Created at: 2019-01-07 11:30:54 UTC  
> Url: https://github.com/boostorg/mpi/issues/80#issuecomment-451906582  

Also note that CC can be any one of gcc, intel, cray compiler, depending on which module is loaded.

---

## Comment 5

> Username: aminiussi  
> Created at: 2019-10-22 15:27:26 UTC  
> Url: https://github.com/boostorg/mpi/issues/80#issuecomment-545018302  

Documentation committed in master.  
Nothing much we can do right now.
