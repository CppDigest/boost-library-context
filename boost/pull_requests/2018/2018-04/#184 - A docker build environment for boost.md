# #184 [NO MERGE] A docker build environment for boost [Closed]

> Username: jeking3  
> Created at: 2018-04-30 00:58:20 UTC  
> Updated at: 2018-11-19 19:06:47 UTC  
> Closed at: 2018-11-19 19:06:47 UTC  
> Url: https://github.com/boostorg/boost/pull/184  

The intention of this pull request is to make a stable docker linux build environment for boost that contains all of the library and documentation build dependencies pre-configured.  See `docker/README.md` for additional details.  
  
### Not working (yet) ###  
- `compute` doesn't have access to OpenCL in the docker instance  
- `mpi` build doesn't seem to want to work despite having openmpi-dev?  
  
### Undecided ###  
- Does this belong in the superproject or in its own submodule?  
- Are the helper scripts useful and common enough to keep or should we just use a readme and have people modify their own local environment with (power)shell scripts customized to their needs?  
  
### More Details ###  
- Start by downloading boost recursively to your host and installing docker.  
- Build a docker container (docker/build.sh).  
- Run a docker container (docker/run.sh), which maps the boost directory on your host to /boost in the container.  
- Do what you would normally do to build.  
  
When you are in a docker instance (that's a running container), everything in the /boost directory is persistent and kept on your host.  Everything else is ephemeral.  So for example if you run bootstrap.sh inside the instance, quit, start a new instance, b2 will be available since it is stored inside /boost.  Your normal development workflow should still apply; builds will be incremental in the instance since it is using a directory on your host to store all of the data.

---

## Review 1 [Approved]

> Username: ericcurtin  
> Created_at: 2018-05-08 12:45:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/boost/pull/184#pullrequestreview-118345275  

Seems like a great idea to me... I'll test later on my machine

---

## Comment 2

> Username: ericcurtin  
> Created_at: 2018-05-08 15:31:53 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-387443880  

I work in a environment that requires a proxy to access public internet, changing the `docker build` line to:  
  
`docker build --build-arg http_proxy --build-arg https_proxy --build-arg ftp_proxy -t ${group}:${distro}-${version} ${scriptdir}/${distro}/${version}`  
  
should ensure both users behind a proxy and not behind a proxy may build.  
  
Once I make this change it works perfectly for me

---

## Comment 3

> Username: ericcurtin  
> Created_at: 2018-05-08 15:51:09 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-387450445  

What would also be nice in a future contribution is if this was synced with a dockerhub account for boost. Then maybe all one would need to do is something like:  
  
`docker/run.sh ubuntu bionic`  
  
and they could pull the image automatically, which is normally faster than building!

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-05-08 16:22:50 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-387460158  

Thanks, good ideas.  I wasn't certain about the usefulness of the scripts to build, run, clean.  I just know I've made them countless times for different projects and as you've suggested everyone has a slightly different set of requirements for the command line.  Certainly the dockerfile(s) and readme are useful, and folks could just add functions to their own .bashrc file instead.  Curious what folks think about that versus having utility scripts to maintain.

---

## Comment 5

> Username: mclow  
> Created_at: 2018-05-08 18:31:17 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-387498834  

Shouldn't you run `bootstrap.sh` and `b2 headers` as part of the build process?  
Everyone needs those, but not everyone needs all the built libraries. (`b2`)

---

## Comment 6

> Username: ericcurtin  
> Created_at: 2018-05-08 19:37:13 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-387517720  

@mclow it's normally best to leave the execution of files in the git repo seperate to the Dockerfile/image since a git repo is relatively dynamic

---

## Comment 7

> Username: mclow  
> Created_at: 2018-05-08 20:30:13 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-387532517  

@ ericcurtin, I can live with that, but since everyone is going to do those two commands before they do *anything else*, I think it makes sense to do it for them.  
  
Again - not going to insist.

---

## Comment 8

> Username: mabrarov  
> Created_at: 2018-05-08 22:07:26 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-387557775  

This may be helpful (while idea is different and targets Windows mostly - to use docker images for building of 3rd party libraries without need to create special build environment for each library): https://github.com/mabrarov/build-scripts/tree/master/docker/boost-msvc-2017. Sorry if it's too far and completely unrelated to this pull request.

---

## Comment 9

> Username: apolukhin  
> Created_at: 2018-05-09 07:58:30 UTC  
> Updated_at: 2018-05-09 07:59:42 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-387655091  

@jeking3 you should also add tools for boulding docs (doxygen and others)  
  
Probably the best way to do it is to run tools/boostbook/setup_boostbook.sh  
  
P.S.: awesome PR! Love it!

---

## Comment 10

> Username: jeking3  
> Created_at: 2018-05-09 12:19:46 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-387720258  

The Dockerfile is designed to set up an environment around boost for building.  Boost is not located in the environment itself.  Here's how it works:  
  
1. You start by downloading boost recursively to your host and installing docker.  
2. You build a docker container (docker/build.sh).  
3. You run a docker container (docker/run.sh), which maps the boost directory on your host to /boost in the container.  
  
So for something like running bootstrap.sh, or b2 headers, or setup_boostbook.sh, that would happen inside the container and not during "docker build".  Given that the boost directory is located on your host, that means the results of "bootstrap.sh" running in the docker container are stored on your host.  The next time you run a docker container (docker/run.sh), b2 is already there because everything in "/boost" is persistent.  Everything outside of "/boost" is ephemeral, so for example if you use apt-get to install a package inside the container, then quit, then start a new one, the package isn't there - however anything in "/boost" remains, including all the build output (since I believe b2 puts build output into ${BOOST_ROOT}/bin.v2, correct?)  
  
Therefore "bootstrap.sh" and "b2 headers" is still a one-time operation.  
If you build in one container instance, then quit and start another container instance, it will only build whatever changed since your last build.  
  
As for setup_boostbook.sh, the packages it would install need to move into the Dockerfile.  Alternatively, if the file is truly standalone, we could copy and even execute the shell script in the Dockerfile... so I'll take a look at that.  
  
Do folks think this belongs in the superproject or should it be in its own submodule?

---

## Comment 11

> Username: jeking3  
> Created_at: 2018-05-13 12:40:47 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-388624025  

I incorporated the proxy command line changes and translated the `setup_boostbook.sh` work into the docker build.  I tested and was able to build libs/date_time/xmldoc inside the container.  I also added a fixup for Ubuntu Bionic targeting clang, since there is no `clang` or `clang++` on that platform (see https://github.com/boostorg/build/issues/306) I provided a user-config.jam that gets placed into the docker container (in `/home/boost/user-config.jam`).  
  
The only problem I see with this is folks using their own `user-config.jam` will need to modify it every time they start up an instance... however ideally changing `user-config.jam` won't be necessary inside the docker environment.

---

## Review 12 [Commented]

> Username: teeks99  
> Created_at: 2018-05-19 12:44:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/184#pullrequestreview-121618194  

📁 docker/ubuntu/bionic/Dockerfile

```diff
  16 |+ 
  17 |+ RUN apt-get update && \
  18 |+     apt-get dist-upgrade -y && \
```

> Username: teeks99  
> Created_at: 2018-05-19 12:44:58 UTC  
> Updated_at: 2018-11-04 12:44:54 UTC  
> Url: https://github.com/boostorg/boost/pull/184#discussion_r189433646  

We shouldn't be doing a dist-upgrade....the FROM ubuntu:bionic means that it should be an up-to-date image.

> Username: jeking3  
> Created_at: 2018-05-21 19:42:06 UTC  
> Updated_at: 2018-11-04 12:44:54 UTC  
> Url: https://github.com/boostorg/boost/pull/184#discussion_r189692493  

Thanks, I can remove that.  I don't think it is harmful, since it will catch ubuntu patches that haven't made it into the upstream docker image, but we don't really need it.


---

## Review 13 [Commented]

> Username: teeks99  
> Created_at: 2018-05-19 12:46:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/184#pullrequestreview-121618212  

📁 docker/ubuntu/bionic/Dockerfile

```diff
  22 |+ # Basic utilities
  23 |+ 
  24 |+ RUN apt-get install -y --no-install-recommends \
```

> Username: teeks99  
> Created_at: 2018-05-19 12:46:04 UTC  
> Updated_at: 2018-11-04 12:44:54 UTC  
> Url: https://github.com/boostorg/boost/pull/184#discussion_r189433668  

Instead of multiple RUN commands for each stanza, can we just have the continuation line continue? This will result in less filesystem unions I believe?

> Username: jeking3  
> Created_at: 2018-05-21 19:43:36 UTC  
> Updated_at: 2018-11-04 12:44:54 UTC  
> Url: https://github.com/boostorg/boost/pull/184#discussion_r189692810  

I'm not sure that's necessary... given the architecture those unions should be highly optimized by now.  For example in the Apache Thrift docker files I think there is one statement per language at a minimum so there have to be at least 35 directives in the Dockerfile, and I don't notice any degradation when I am running.  (Of course, I'm usually running on flash...)


---

## Review 14 [Commented]

> Username: teeks99  
> Created_at: 2018-05-19 12:49:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/184#pullrequestreview-121618283  

📁 docker/ubuntu/bionic/user-config.jam

```diff
  11 |+ using clang : 6.0 : /usr/bin/clang++-6.0 ;
  12 |+ using gcc : 7.3 : /usr/bin/g++-7 ;
  13 |+ 
```

> Username: teeks99  
> Created_at: 2018-05-19 12:49:47 UTC  
> Updated_at: 2018-11-04 12:44:54 UTC  
> Url: https://github.com/boostorg/boost/pull/184#discussion_r189433744  

do we want to add more compiler configurations for users to work with, such as  
  
```  
using gcc : 7~c++17 : /usr/bin/g++-7 : <cxxflags>"-std=c++17" ;  
using gcc : 7~gnu17 : /usr/bin/g++-7 : <cxxflags>"-std=gnu++17" ;  
using gcc : 7~c++2a : /usr/bin/g++-7 : <cxxflags>"-std=c++2a" ;  
using gcc : 7~gnu2a : /usr/bin/g++-7 : <cxxflags>"-std=gnu++2a" ;  
using gcc : 7~c++2a~O2 : /usr/bin/g++-7 : <cxxflags>"-std=c++2a -O2" ;  
using gcc : 7~c++2a~warn : /usr/bin/g++-7 : <cxxflags>"-std=c++2a -Wall -Wextra" ;  
  
```

> Username: mclow  
> Created_at: 2018-05-19 13:05:01 UTC  
> Updated_at: 2018-11-04 12:44:54 UTC  
> Url: https://github.com/boostorg/boost/pull/184#discussion_r189434036  

I used to do this, but I believe the new hotness is to specify it on the command line:  
  
    ./b2 --toolset=gcc/cxxstd=17

> Username: jeking3  
> Created_at: 2018-05-21 19:41:25 UTC  
> Updated_at: 2018-11-04 12:44:54 UTC  
> Url: https://github.com/boostorg/boost/pull/184#discussion_r189692327  

I added the user-config.jam because without it I couldn't say "<toolset>gcc" or "<toolset>clang" and have it work from within the docker instance.  For clang this is because the clang selector logic in bjam is pretty old and hasn't been updated with the gcc version selector "hotness".  I'd prefer not to have a user-profile.jam at all inside the docker instance and instead use toolset=clang-6 cxxstd=14 cxxstd-dialect=gnu, if you want to use /usr/bin/clang++-6 with -std=gnu++14.  
  
In fact I think it might be ideal in the docker environment to take the user-config.jam from the user's home directory and put it into the docker instance at startup time... that way you can manage your own settings the way you are used to managing them, without having to copy things around yourself after you spin up the docker instance.


---

## Comment 15

> Username: pdimov  
> Created_at: 2018-10-23 19:36:55 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-432389251  

Does this need to reside in the superproject? I'd think `tools/docker` and a submodule boostorg/docker may be better suited?  
  
Not that I know anything about Docker, so I could be wrong.

---

## Comment 16

> Username: jeking3  
> Created_at: 2018-10-27 13:56:45 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-433622591  

@pdimov that was one of the open questions (see the description).  The docker environment makes it easier for someone who doesn't have a development environment for boost to get started by providing all the dependencies in a neat package.  If the project maintains it as a tool as you stated, then folks can count on it being maintained.  
  
If it is maintained as a submodule then it might be easier to integrate with Docker Hub (as Docker Hub could be taught to build just that repository to maintain the images for people to get, once we have an official Docker Hub account I can use...).

---

## Comment 17

> Username: jeking3  
> Created_at: 2018-10-27 14:44:22 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-433626613  

Some additional things I noticed today that need to be added, resolve as many of the `no`'s below:  
```  
    - libfftw3                 : no  
    - __float128               : no  
    - Intel _Quad datatype support : no  
    - has_ntl_rr builds        : no  
    - has_mpfr_class builds    : no  
    - has_mpreal builds        : no  
    - has_e_float builds       : no  
    - 128-bit floatmax_t       : no  
    - BOOST_MULTI_INDEX_KEY_SUPPORTED defined : no  
    - has_gmp builds           : yes  
    - has_mpfr builds          : no  
    - has_tommath builds       : no  
    - has_mpfi builds          : no  
    - has_float128 builds      : no  
    - has_intel_quad builds    : no  
    - has_mpc builds           : no  
```

---

## Comment 18

> Username: jeking3  
> Created_at: 2018-11-19 19:06:45 UTC  
> Url: https://github.com/boostorg/boost/pull/184#issuecomment-440006387  

This effort has morphed into:  
  
https://github.com/jeking3/bdde  
  
As such I am going to close this out.

---
