# #554 - Unable to compile Boost v1.72.0 for arm/v6 and arm/v7 [Open]

> Username: avwarez  
> Created at: 2020-03-24 21:00:04 UTC  
> Updated at: 2021-05-29 18:22:20 UTC  
> Url: https://github.com/boostorg/build/issues/554  

Hi,  
I'm trying to build Boost v1.72.0 using docker buildx without success. The same Dockerfile (so, the same sources) works and I've succesfull built image for arm64, i386 and amd64. For all my images I use debian buster/slim.  
  
The error details:  
`#7 [stage-0 4/7] RUN cd /usr/src/boost-git/tools/build &&     ./bootstrap.s...  
boostorg/boost#7 2.228 Bootstrapping the build engine with toolset gcc...  
boostorg/boost#7 184.2  
boostorg/boost#7 184.2 Bootstrapping is done. To build and install, run:  
boostorg/boost#7 184.2  
boostorg/boost#7 184.2     ./b2 install --prefix=<DIR>  
boostorg/boost#7 184.2  
boostorg/boost#7 DONE 184.6s  
  
boostorg/boost#8 [stage-0 5/7] RUN cd /usr/src/boost-git/tools/build &&     ./b2 install ...  
boostorg/boost#8 0.653 Unable to load Boost.Build: could not find build system.  
boostorg/boost#8 0.653 ---------------------------------------------------------  
boostorg/boost#8 0.653 /usr/src/boost-git/tools/build/boost-build.jam attempted to load the build system by invoking  
boostorg/boost#8 0.653  
boostorg/boost#8 0.653    'boost-build src/kernel ;'  
boostorg/boost#8 0.653  
boostorg/boost#8 0.653 but we were unable to find "bootstrap.jam" in the specified directory  
boostorg/boost#8 0.654 or in BOOST_BUILD_PATH (searching /usr/src/boost-git/tools/build/src/kernel, /usr/share/boost-build).  
boostorg/boost#8 0.654  
boostorg/boost#8 0.656 Please consult the documentation at 'http://www.boost.org'.  
boostorg/boost#8 ERROR: executor failed running [/bin/sh -c cd /usr/src/boost-git/tools/build &&     ./b2 install &&     cd /usr/src/boost-git &&     b2 toolset=gcc install cxxflags=-std=c++14 --prefix=/app --with-system --with-chrono --with-random]: buildkit-runc did not terminate sucessfully  
------  
 > [stage-0 5/7] RUN cd /usr/src/boost-git/tools/build &&     ./b2 install &&     cd /usr/src/boost-git &&     b2 toolset=gcc install cxxflags=-std=c++14 --prefix=/app --with-system --with-chrono --with-random:  
------  
failed to solve: rpc error: code = Unknown desc = failed to solve with frontend dockerfile.v0: failed to build LLB: executor failed running [/bin/sh -c cd /usr/src/boost-git/tools/build &&     ./b2 install &&     cd /usr/src/boost-git &&     b2 toolset=gcc install cxxflags=-std=c++14 --prefix=/app --with-system --with-chrono --with-random]: buildkit-runc did not terminate sucessfully  
time="2020-03-24T16:22:44+01:00" level=warning msg="No output specified for docker-container driver. Build result will only remain in the build cache. To push result image into registry use --push or to load image into docker use --load"`  
  
If you need, my Dockerfile can be found here: [qBittorrent-docker](https://github.com/avwarez/qBittorrent-docker)  
  
Thanks in advance

---

## Comment 1

> Username: GregKon  
> Created at: 2020-03-27 06:58:58 UTC  
> Url: https://github.com/boostorg/build/issues/554#issuecomment-604845937  

Hi  
  
what environment you are using for ARM6?  
/Greg

---

## Comment 2

> Username: avwarez  
> Created at: 2020-03-28 11:07:25 UTC  
> Url: https://github.com/boostorg/build/issues/554#issuecomment-605431938  

> Hi  
>   
> what environment you are using for ARM6?  
> /Greg  
  
Hi GregKon,  
the environment is the same for all architecture amd64, arm64, 386, armv6 and armv7 (gcc 8.3). My docker image is built from official debian buster slim, for armv6 it uses armel architecture.  
  
I hope I understood your question :) If you need I can provide steps to reproduce the issue.  
  
Thanks

---

## Comment 3

> Username: GregKon  
> Created at: 2020-03-28 11:19:29 UTC  
> Url: https://github.com/boostorg/build/issues/554#issuecomment-605433059  

Hi  
I am just asking, because I would like to build boost test for IAR compiler. I have small success but still looking some help with b2 tool.  
/BR

---

## Comment 4

> Username: avwarez  
> Created at: 2020-03-28 11:31:58 UTC  
> Updated at: 2020-03-28 11:32:27 UTC  
> Url: https://github.com/boostorg/build/issues/554#issuecomment-605434292  

Ok.  
  
I think steps to reproduce can be useful, so..  
prepare the docker env (for cross-compilation):  
1. docker run --rm --privileged docker/binfmt:a7996909642ee92942dcd6cff44b9b95f08dad64  
2. docker buildx create --name mybuilder  
3. docker buildx use mybuilder  
4. docker buildx inspect --bootstrap  
  
start compilation (arm/v6, but you can use it for all architectures supported from your kernel):  
1. Download a Dockerfile with compile instructions inside it (for example the mine for qBittorrent)  
2. docker buildx build --platform linux/arm/v6 -t compile-test-armv6 .

---

## Comment 5

> Username: avwarez  
> Created at: 2020-04-06 16:49:03 UTC  
> Url: https://github.com/boostorg/build/issues/554#issuecomment-609910100  

Hello,  
 I think the problem depends by the Linux distribution. I've changed the Dockerfile and using Alpine Linux as base image the issue is solved. So the problem is actually present only with Debian.  
Thanks

---

## Comment 6

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:50 UTC  
> Url: https://github.com/boostorg/build/issues/554#issuecomment-850877274  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
