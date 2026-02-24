# #281 Add support for s390x [Merged]

> Username: nealef  
> Created at: 2019-05-01 13:52:05 UTC  
> Updated at: 2019-06-26 07:37:44 UTC  
> Merged at: 2019-06-26 07:37:44 UTC  
> Closed at: 2019-06-26 07:37:44 UTC  
> Url: https://github.com/boostorg/config/pull/281  

Include s390x as a supported architecture

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-05-01 16:37:03 UTC  
> Url: https://github.com/boostorg/config/pull/281#issuecomment-488335481  

I'm curious what the point of this PR is: adding an architecture test to Boost.Config is only relevant if some Boost library needs different _build_ behaviour on that platform: as in "don't build Boost.Whatever on s390x".

---

## Comment 2

> Username: nealef  
> Created_at: 2019-05-01 17:34:36 UTC  
> Url: https://github.com/boostorg/config/pull/281#issuecomment-488352413  

It seems required when running a build of the boost rpm (I backported the changes to the level 1.69 to verify the building of an RPM). Without it I get:  
```  
+ ./b2 -d+2 -q -j3 --without-mpi --without-graph_parallel --build-dir=serial variant=release threading=multi debug-symbols=on pch=off python=2.7 stage  
MPI auto-detection failed: unknown wrapper compiler mpic++  
Please report this error to the Boost mailing list: http://www.boost.org  
You will need to manually configure MPI support.  
Performing configuration checks  
  
    - default address-model    : 64-bit  
error: at /root/rpmbuild/BUILD/boost_1_69_0/tools/build/src/kernel/modules.jam:107  
error: Unable to find file or target named  
error:     '/boost/architecture//s390x'  
error: referred to from project at  
error:     '.'  
```  
With it, I get a clean build.

---

## Comment 3

> Username: mjcaisse  
> Created_at: 2019-06-25 15:28:39 UTC  
> Url: https://github.com/boostorg/config/pull/281#issuecomment-505496706  

@jzmaddock do you have any other thoughts on this? Support has been merged into b2 (4.0.0) and there is an outstanding PR for the super project for support also.

---

## Comment 4

> Username: nealef  
> Created_at: 2019-06-25 15:31:12 UTC  
> Url: https://github.com/boostorg/config/pull/281#issuecomment-505497780  

I have a number of PRs almost ready for the various bits of the boost suite. I have been busy on some work projects that have taken my time away from this but I will be revisiting it when things settle down. The main PR will contain the fcontext support.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2019-06-26 07:37:39 UTC  
> Url: https://github.com/boostorg/config/pull/281#issuecomment-505758259  

OK, merging.

---
