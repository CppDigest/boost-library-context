# #355 - Support QEMU CI testing. [Closed]

> Username: grafikrobot  
> Created at: 2018-10-12 23:30:56 UTC  
> Updated at: 2023-12-24 03:03:19 UTC  
> Closed at: 2023-12-24 03:03:19 UTC  
> Url: https://github.com/boostorg/build/issues/355  

Investigate making it possible to launch a QEMU image to test b2 in various operating systems.

---

## Comment 1

> Username: jeking3  
> Created at: 2019-06-10 14:46:06 UTC  
> Url: https://github.com/boostorg/build/issues/355#issuecomment-500443691  

I'm working on something like this in the Boost Docker Development Environment.  I can run aarch64 (arm64) and ppc64le environments through multiarch.  I'm trying to get a big-endian environment running.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2019-06-10 15:13:25 UTC  
> Url: https://github.com/boostorg/build/issues/355#issuecomment-500453943  

This task may be easier now that we are using AZP for testing. We can at minimum have self-hosted agents in the mix that run the extra OSes (be it QEMU or any other VM). We would need a way to control QEMU from outside from a Linux (or Windows, or OSX) environment. And of course if that other "hardware" you are talking about is running one of the supported Linux variants they could be added directly as self-hosted agents.

---

## Comment 3

> Username: jeking3  
> Created at: 2019-06-11 22:06:30 UTC  
> Url: https://github.com/boostorg/build/issues/355#issuecomment-501041194  

I can run big-endian containers on little-endian hosts now, so it's only a matter of time before we get the CI provider scripts configured to do the same.  https://github.com/jeking3/bdde

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:03 UTC  
> Url: https://github.com/boostorg/build/issues/355#issuecomment-859203341  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
