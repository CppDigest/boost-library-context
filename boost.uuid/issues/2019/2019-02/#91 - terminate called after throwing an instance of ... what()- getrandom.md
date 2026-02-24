# #91 - terminate called after throwing an instance of ... what():  getrandom [Closed]

> Username: kohldampfer  
> Created at: 2019-02-07 08:52:27 UTC  
> Updated at: 2019-02-13 13:22:55 UTC  
> Closed at: 2019-02-13 13:22:55 UTC  
> Url: https://github.com/boostorg/uuid/issues/91  

I comiled a project against boost library 1.68. When starting this project, I get this error message  
  
```  
bash-4.2# ./run.sh  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::uuids::entropy_error> >'  
  what():  getrandom  
Aborted  
```  
  
After some googling I found out that this error has something to do with the syscall `getrandom` and that the syscall was introduced in Linux kernel >= 3.17. On the machine, where I start this project, I have this kernel (it's a CentOS 6.7):  
  
> bash-4.2# uname -a  
> Linux xxx 2.6.32-754.10.1.el6.x86_64 #1 SMP Tue Jan 15 17:07:28 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux  
  
> bash-4.2# cat /etc/*-release  
> CentOS release 6.7 (Final)  
  
Then I found out that I can rebuild boost with the parameter `BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX`, then I could start my project (https://github.com/boostorg/uuid/issues/79). So I recompiled boost 1.68. This are the commands in a self-written build script:  
  
  
> ./bootstrap.sh  --with-libraries=atomic,chrono,container,context,contract,coroutine,date_time,exception,fiber,filesystem,graph,graph_parallel,iostreams,locale,log,math,mpi,program_options,random,regex,serialization,stacktrace,system,test,thread,type_erasure,timer,signals,wave  
>   
> ./b2 --layout=tagged define=BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX toolset=gcc variant=release threading=multi link=shared address-model=64 -sZLIB_SOURCE=$ZLIB_DIR -q -sBZIP2_SOURCE=$BZIP2_DIR  
  
After recompilation of boost 1.68 and my script, I still get this error written above. Is there something I am missing here?

---

## Comment 1

> Username: Lastique  
> Created at: 2019-02-07 17:11:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/91#issuecomment-461515173  

Duplicates https://github.com/boostorg/uuid/issues/79.  
  
You have to use Linux kernel headers matching (or at least, not newer than) the kernel you're running. Otherwise you have to explicitly disable `getrandom` when building both Boost *and your code* that uses Boost by defining `BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX`.

---

## Comment 2

> Username: kohldampfer  
> Created at: 2019-02-07 17:13:44 UTC  
> Url: https://github.com/boostorg/uuid/issues/91#issuecomment-461516079  

@Lastique When calling `b2`, I added the parameter `define=BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX` like written above. As far as I know, this is the way to explicitly disable `getrandom`, right?

---

## Comment 3

> Username: Lastique  
> Created at: 2019-02-07 17:18:31 UTC  
> Url: https://github.com/boostorg/uuid/issues/91#issuecomment-461517874  

No, not quite. Boost.UUID is a header-only library. The `b2` command builds Boost. The `define=BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX` in that command ensures that if any compiled Boost library uses Boost.UUID internally, it will be built without using `getrandom`. An equivalent define needs to be added for any project that uses Boost.UUID (directly or indirectly) as well.

---

## Comment 4

> Username: kohldampfer  
> Created at: 2019-02-13 01:13:46 UTC  
> Url: https://github.com/boostorg/uuid/issues/91#issuecomment-463012895  

@Lastique Thank you for your answer. This sentence `An equivalent define needs to be added for any project that uses Boost.UUID (directly or indirectly) as well.` was the missing link.  
  
But there is another question. I was building boost 1.68 on a CentOS 7.6 system. This system has this kernel version:  
  
> $ uname -a  
> Linux local 3.10.0-957.5.1.el7.x86_64 #1 SMP Wed Dec 19 10:46:58 EST 2018 x86_64 x86_64 x86_64 GNU/Linux  
  
and this glibc version:  
  
> $ ldd --version  
> ldd (GNU libc) 2.17  
  
Why is the error occuring (described above) on another machine? I mean, [when looking into the code of the file random_provider_detect_platform.hpp](https://github.com/boostorg/uuid/blob/boost-1.68.0/include/boost/uuid/detail/random_provider_detect_platform.hpp), there is this preprocessor check  
  
```  
#elif BOOST_OS_LINUX && defined(SYS_getrandom) && defined(BOOST_UUID_RANDOM_PROVIDER_FORCE_POSIX) && !defined(BOOST_UUID_RANDOM_PROVIDER_DISABLE_GETRANDOM)  
```  
  
As also written in https://github.com/boostorg/uuid/issues/79, [the syscall was introduced in kernel >= 3.17 and support was added in glibc version 2.25](http://man7.org/linux/man-pages/man2/getrandom.2.html). But when the syscall was introduced in a kernel later than the kernel and glibc version I built boost on, why the preprocessor took this path and activated the `getrandom` syscall? Somehow `SYS_getrandom` was defined on the system... but why?  
  
I do not understand why the part `defined(SYS_random)` gets true on a kernel / glibc version older than the version where `getrandom` was introduced.

---

## Comment 5

> Username: Lastique  
> Created at: 2019-02-13 09:39:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/91#issuecomment-463128360  

The syscall numbers are defined by kernel headers, which are not part of glibc but part of kernel distribution. The fact that the macro is defined while you're running an older kernel means that your installed kernel headers are for a newer kernel than you're running.

---

## Comment 6

> Username: kohldampfer  
> Created at: 2019-02-13 13:22:55 UTC  
> Url: https://github.com/boostorg/uuid/issues/91#issuecomment-463197297  

RedHat had a backport for the syscall: https://bugzilla.redhat.com/show_bug.cgi?id=1330000  
This answers my question.  
  
Thank you for your time and your answers.
