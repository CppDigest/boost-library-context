# #642 - ci: Switch CI configuration to boostorg/boost-ci [Open]

> Username: mloskot  
> Created at: 2022-04-07 14:52:55 UTC  
> Updated at: 2022-05-20 09:12:03 UTC  
> Url: https://github.com/boostorg/gil/issues/642  

Travis CI is dead. Our CI has rotten. We should consider optimising and eliminate inefficient, repeated builds, improve CI maintenance.  
  
The https://github.com/boostorg/boost-ci offers Boost-standardized configuration for all major CI services.  
It is compelling to switch our home-grown configuration to it. So, let's discuss it.  
  
### Major issues with boost-ci that we need to address:  
  
- We need to disable certain compilers (e.g. GCC <5) and we need to comment entries in YAML files - we have to modify the boost-ci files anyway  
- We need to build multiple targets e.g. separate core from I/O, or run I/O tests iff core tests pass, but boost-ci always builds `b2 libs/<lib>/test`.  
- We need to install third-party dependencies for I/O (using `apt`, `conan` or `vcpkg`)  
- ...  
  
### Playground  
  
- https://github.com/mloskot/gil/tree/ci branch with boost-ci for GIL prototype   
- https://github.com/mloskot/gil/pull/3 as playground

---

## Comment 1

> Username: striezel  
> Created at: 2022-04-07 16:25:17 UTC  
> Updated at: 2022-04-07 16:36:25 UTC  
> Url: https://github.com/boostorg/gil/issues/642#issuecomment-1091954161  

> * We need to disable certain compilers  
  
Yeah. I'd like to remove Clang 3.5, 3.6, 3.7 and 3.8 from the GitHub Actions (=GHA) workflows and would have done so already in #640 to get rid of building on Ubuntu 16.04 completely. GHA does not support Ubuntu 16.04 anymore anyway, so keeping it is not a viable option. And Clang version 3.8 and earlier are not available via the currently used APT repository anymore, even when switching to a newer Ubuntu version for builds. This means that there is no "easy" way to install those compilers via `apt-get` during those workflows. However, I am not sure what guarantees Boost GIL or Boost in general makes to its users for supporting older compilers, so I decided not to remove the older Clang builds as part of #640.  
  
For reference: <https://releases.llvm.org/>. Clang 3.5 was released in September 2014, almost eight years ago. Clang 3.8 was released in March 2016, i. e. six years ago.  
  
**PS:** As a general idea, we should probably make sure that the existing CI pipelines pass (except for the outdated stuff that may potentially get removed). Otherwise we may just be migrating existing build failures to another CI configuration with boost-ci.

---

## Comment 2

> Username: mloskot  
> Created at: 2022-04-07 18:46:22 UTC  
> Updated at: 2022-04-07 18:47:21 UTC  
> Url: https://github.com/boostorg/gil/issues/642#issuecomment-1092085794  

> PS: As a general idea, we should probably make sure that the existing CI pipelines pass (except for the outdated stuff that may  
> potentially get removed). Otherwise we may just be migrating existing build failures to another CI configuration with boost-ci.  
  
Yes, this is an ideal plan. We, however, do what we can i.e. w.r.t. man power. So, your contributions to our CI are godsend.  
  
> I'd like to remove Clang 3.5, 3.6, 3.7 and 3.8 from the GitHub Action (...)  
  
First, let's summarise where we currently are:  
  
  1. Boost.GIL became a C++11 library in 2018 (after [grand merge](https://lists.boost.org/boost-gil//2018/07/0048.php) and we still have a few items to do, see [C++11 Modernization](https://github.com/boostorg/gil/projects/3) plan).  
  
  2. We have dropped support for GCC 4.8 after I asked the Boost community  
      > what is usable version of GCC for C++11 support and the answer was: 4.8 core, 4.9 library, 5 without bugs  
  
      See https://github.com/boostorg/gil/issues/282  
  
  3. We are going to bump the minimum required C++ version and we have already issued the deprecation notice with Boost 1.75.0 (see https://github.com/boostorg/gil/blob/develop/RELEASES.md) - see discussion at https://github.com/boostorg/gil/pull/526.   
  
We should follow similar plan w.r.t. to clang and any other compiler we support.   
We should not arbitrarily or accidentally remove support for any compiler without prior notice.  
  
It is likely that we will require C++17 as minimum standard some time soonish, but we need to be considerate and well-behaving part of Boost.  
  
See the 2020 thread [Shall we switch to C++17?](https://lists.boost.org/boost-gil//2020/10/0466.php)/10/0465.php), where the most important part to consider is Peter Dimov's [sensible suggestion ](https://pdimov.github.io/articles/phasing_out_cxx03.html) that applies to any C++ version, not just C++03:  
  
> The suggested way forward is to allow library authors to declare C++03 support deprecated via a notice in the documentation and a message issued at compile time, then be allowed to drop C++03 support no earlier than two Boost releases later.  
  
/cc @boostorg/gil-developers

---

## Comment 3

> Username: sdebionne  
> Created at: 2022-04-07 19:30:50 UTC  
> Url: https://github.com/boostorg/gil/issues/642#issuecomment-1092123319  

I am all in to move to Boost.CI and standardized configuration that would help with the maintenance. I prefer a limited set of compilers:configurations to a CI that fails for obscure reasons.  
  
> It is likely that we will require C++17 as minimum standard some time soonish  
  
OK with me, and I agree that we need to move forward with respect to the community.  
  
As a user of Boost.GIL, I get my dev env (and the Boost libraries) from Conda and  can use the latest compilers on all platforms, so dropping support for 5yo compilers is not a problem. I am not saying that we should.

---

## Comment 4

> Username: mloskot  
> Created at: 2022-04-08 10:46:02 UTC  
> Url: https://github.com/boostorg/gil/issues/642#issuecomment-1092730524  

> I prefer a limited set of compilers:configurations to a CI that fails for obscure reasons.  
  
I agree.   
  
Ideally, if the CI is defined in some sort of fan-out fashion like [Boost.Geometry build on CircleCI](https://app.circleci.com/pipelines/github/boostorg/geometry/286/workflows/bf8ae61c-76a6-4942-8f6f-3e0e85fda02f), with inter-job or even inter-workflow dependencies,  
  
For example, our GitHub Actions could run sequence of inter-dependant workflows:  
  1. `minimal-core.yml` - build with single (first fullly C++17-compliant) version of GCC and Clang and MSVC against GIL core tests  
  2. `headers-core.yml` - self-contained headers check  
  3. `minimal-ext.yml` - like `minimal-core.yml` but for extensions (w/ simple I/O tests)  
  4. `headers-ext.yml` - like `headers-core.yml` but for extensions  
  5. `full-core.yml` - core tests build with large variety of compilers/versions, `cxxstd`-s, build `variant`-s and `address-model`-s  
  6. `full-ext.yml` - like `full-core.yml` but for extensions (w/ full I/O tests)  
    
I think this may be possible with GHA features like dependant jobs and `workflow_call` or `workflow_run`.  
  
Such approach, which I think I'm in favour of, would justify resignation from the switch to boost-ci as we would have to have full control on the GHA workflows.

---

## Comment 5

> Username: sdebionne  
> Created at: 2022-04-13 06:51:08 UTC  
> Url: https://github.com/boostorg/gil/issues/642#issuecomment-1097623174  

I think it's a good idea to have separate pipelines for core / extensions and different groups of compilers / std. It will give use a much better idea of the location / severity of failing pipelines. I understand that Boost.CI is not flexible enough to implement this approach though.

---

## Comment 6

> Username: mloskot  
> Created at: 2022-05-20 09:12:03 UTC  
> Url: https://github.com/boostorg/gil/issues/642#issuecomment-1132668090  

I have moved the planning towards C++14/17 to discussion here https://github.com/boostorg/gil/discussions/676
