# #470 Bring back with-toolset applying to b2 build. [Merged]

> Username: grafikrobot  
> Created at: 2021-01-25 20:44:47 UTC  
> Updated at: 2021-02-09 14:52:35 UTC  
> Merged at: 2021-02-09 14:52:35 UTC  
> Closed at: 2021-02-09 14:52:35 UTC  
> Url: https://github.com/boostorg/boost/pull/470  

I've now gotten a few requests to restore the ability to specify the toolset that b2 is built with through the Boost root bootstrap.sh script. This change brings that aspect back. But it also clarifies what that option does and the ensuing results in the output.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2021-01-25 20:45:59 UTC  
> Url: https://github.com/boostorg/boost/pull/470#issuecomment-767101908  

FYI @pdimov & @Flamefire

---

## Comment 2

> Username: Flamefire  
> Created_at: 2021-01-26 07:57:32 UTC  
> Url: https://github.com/boostorg/boost/pull/470#issuecomment-767370085  

How about using this opportunity to change the bootstrap description from   
> `./bootstrap.sh' prepares Boost for building on a few kinds of systems.  
  
to something useful?  
  
>  `./bootstrap.sh' builds the Boost build system B2 and prepares Boost for building  
> This includes setting defaults in the project-config.jam which can be adjusted by user prior to invoking B2  
  
(or so, mind typos or filename mixups)

---

## Comment 3

> Username: sergiu128  
> Created_at: 2021-02-02 11:16:39 UTC  
> Url: https://github.com/boostorg/boost/pull/470#issuecomment-771563890  

@grafikrobot I was trying to build boost with clang but it always default to gcc. I think in order to build b2 with the specified toolset, `build.sh` needs to receive that as an argument [here](https://github.com/boostorg/boost/blob/451377a3ff4e0f3c104dbbe53da13a822309f448/bootstrap.sh#L226). So something like:  
```bash  
(cd "$my_dir/tools/build/src/engine" && ./build.sh $TOOLSET)  
```

---

## Comment 4

> Username: Flamefire  
> Created_at: 2021-02-02 11:18:23 UTC  
> Url: https://github.com/boostorg/boost/pull/470#issuecomment-771564812  

@sergiu128 This is exactly what this PR does. Have you tried it?

---

## Comment 5

> Username: mloskot  
> Created_at: 2021-02-02 21:02:11 UTC  
> Updated_at: 2021-02-02 21:02:21 UTC  
> Url: https://github.com/boostorg/boost/pull/470#issuecomment-771983537  

I confirm this PR fixes the _always_ "Using 'gcc' toolset.", at least for me on Ubuntu 20.04 with both, GCC and clang, installed from its packages:  
  
```  
./bootstrap.sh --with-toolset=clang  
Building B2 engine..  
  
###  
###  
### Using 'clang' toolset.  
###  
###  
  
clang version 10.0.0-4ubuntu1  
Target: x86_64-pc-linux-gnu  
Thread model: posix  
InstalledDir: /usr/bin  
...  
```

---
