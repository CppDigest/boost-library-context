# #164 - Make unique_path more portable (GLIBC >=2.25 + Linux < 3.17) [Closed]

> Username: ingomueller-net  
> Created at: 2020-10-25 08:16:39 UTC  
> Updated at: 2020-10-26 09:03:35 UTC  
> Closed at: 2020-10-26 08:56:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/164  

I am running a CI setup that runs in a Docker image with a recent GLIBC (>=2.25) on host with an old Linux kernel (<3.17). In this setup, any call to `unique_path` fails since Boost 1.74 with a `Function not implemented` exception. The problem is that the availability of `getrandom` is determined [the following lines](https://github.com/boostorg/filesystem/blob/boost-1.74.0/src/unique_path.cpp#L35) at compile time:  
  
```c++  
#      if defined(__has_include)  
#          if __has_include(<sys/random.h>)  
#              define BOOST_FILESYSTEM_HAS_GETRANDOM  
#          endif  
#      elif defined(__GLIBC__)  
#          if __GLIBC_PREREQ(2, 25)  
#              define BOOST_FILESYSTEM_HAS_GETRANDOM  
#          endif  
#      endif  
```  
  
When the program is compiled, both checks succeed due to the new GLIBC version in the docker image. However, at runtime, [`getrandom`](https://man7.org/linux/man-pages/man2/getrandom.2.html) calls a syscall that the host kernel does not offer.  
  
I see two solutions: (1) add a compile flag that allows choosing the source of randomness or (2) catch the `not implemented` error at runtime and use the next source if that happens.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-10-25 13:56:36 UTC  
> Url: https://github.com/boostorg/filesystem/issues/164#issuecomment-716152504  

On October 25, 2020 11:16:50 AM Ingo Müller <notifications@github.com> wrote:  
  
> I am running a CI setup that runs in a Docker image with a recent GLIBC   
> (>=2.25) on host with an old Linux kernel (<3.17). In this setup, any call   
> to `unique_path` fails since Boost 1.74 with a `Function not implemented`   
> exception. The problem is that the availability of `getrandom` is   
> determined [the following   
> lines](https://github.com/boostorg/filesystem/blob/boost-1.74.0/src/unique_path.cpp#L35)   
> at compile time:  
>  
> ```c++  
> #      if defined(__has_include)  
> #          if __has_include(<sys/random.h>)  
> #              define BOOST_FILESYSTEM_HAS_GETRANDOM  
> #          endif  
> #      elif defined(__GLIBC__)  
> #          if __GLIBC_PREREQ(2, 25)  
> #              define BOOST_FILESYSTEM_HAS_GETRANDOM  
> #          endif  
> #      endif  
> ```  
>  
> When the program is compiled, the second check succeeds due to the new   
> GLIBC version in the docker image. However, at runtime,   
> [`getrandom`}(https://man7.org/linux/man-pages/man2/getrandom.2.html) calls   
> a syscall that the host kernel does not offer.  
>  
> I see two solutions: (1) add a compile flag that allows choosing the source   
> of randomness or (2) catch the `not implemented` error at runtime and use   
> the next source if that happens.  
  
This can only happen if libc and/or Boost were compiled with kernel headers   
not matching the runtime kernel version. I don't think I want to support   
this kind of mismatch. Please use the matching headers, and this should not   
happen.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-10-25 15:04:51 UTC  
> Url: https://github.com/boostorg/filesystem/issues/164#issuecomment-716162688  

In terms of Docker, my advice would read as "run a Docker image that was based on the same or older kernel version as the host."

---

## Comment 3

> Username: ingomueller-net  
> Created at: 2020-10-26 08:56:19 UTC  
> Url: https://github.com/boostorg/filesystem/issues/164#issuecomment-716407568  

@Lastique: Thanks a lot for the quick reply. After some thinking, I came to the conclusion that you are right. This is a design flaw or at least a drawback of docker that can happen with any software.  
  
Unfortunately, this problem isn't mentioned very prominently (a few places mentioning the problem: [1](https://forums.docker.com/t/libc-incompatibilities-when-will-they-emerge/9895/4), [2](https://stackoverflow.com/a/32842491/651937)). It seems like running a newer kernel on the host than on the target is *better* than the reverse (which is what I had done), but no guarantee for full compatibility. The safest is to run the same distro version on the host and in the container. That somewhat limits the applicability of docker, though...

---

## Comment 4

> Username: ingomueller-net  
> Created at: 2020-10-26 09:03:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/164#issuecomment-716411442  

I just found a good explanation for glibc/kernel compatability [here](https://unix.stackexchange.com/questions/9705/can-new-glibc-versions-be-used-with-an-old-kernel).
