# #31 - boost/accumulators/statistics/density.hpp does not compile [Closed]

> Username: lrineau  
> Created at: 2019-05-10 16:29:39 UTC  
> Updated at: 2019-05-17 12:41:33 UTC  
> Closed at: 2019-05-17 12:41:32 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31  

The file `<boost/accumulators/statistics/density.hpp>` does not compile, in `origin/develop` (1b1995f9a44cdac8bbc9eb60b099ae40342e3977):  
  
```shellsession  
[lrineau@bonnard]~/Git/boost/libs/accumulators% g++ -I ../../install/include -fsyntax-only -xc++ include/boost/ac[lrineau@bonnard]~/Git/boost/libs/accumulators% g++ -I ../../install/include -fsyntax-only -xc++ include/boost/accumulators/statistics/density.hpp  
In file included from include/boost/accumulators/statistics/density.hpp:16:  
../../install/include/boost/parameter/keyword.hpp: In instantiation of ‘struct boost::parameter::keyword<boost::accumulators::tag::density_cache_size_<> >’:  
include/boost/accumulators/statistics/density.hpp:35:1:   required from here  
../../install/include/boost/parameter/keyword.hpp:99:13: error: no type named ‘qualifier’ in ‘struct boost::accumulators::tag::density_cache_size_<>’  
             operator=(T const& x) const  
             ^~~~~~~~  
```  
  
Cc @CromwellEnage It is related to the new Boost Parameter library.

---

## Comment 1

> Username: CromwellEnage  
> Created at: 2019-05-11 02:43:24 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-491473063  

The new additions to Boost.Parameter did not make the 1.70 release, but they are in both the 'master' and 'develop' branches now.

---

## Comment 2

> Username: lrineau  
> Created at: 2019-05-13 15:39:43 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-491875125  

I know that. [My report] is that there is a compilation error in current `develop` version of `<boost/accumulators/statistics/density.hpp>`. I mentioned you, @CromwellEnage, because the compilation error points to changes in Boost Parameter (in `develop`).  
  
[My report]: https://github.com/boostorg/accumulators/issues/31#issue-442800219

---

## Comment 3

> Username: CromwellEnage  
> Created at: 2019-05-13 20:06:38 UTC  
> Updated at: 2019-05-13 21:36:51 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-491966530  

@lrineau, is your copy of `<boost/accumulators/accumulators_fwd.hpp>` from the current `develop` branch?

---

## Comment 4

> Username: lrineau  
> Created at: 2019-05-14 13:23:12 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-492235355  

Yes. It is from the commit ff9432f97b0f08fb4d2cdaf645e6c2735c55459e, which is the current tip of the `develop` branch.

---

## Comment 5

> Username: lrineau  
> Created at: 2019-05-14 13:39:13 UTC  
> Updated at: 2019-05-14 13:39:21 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-492241447  

Actually, all the Boost headers using `BOOST_PARAMETER_NESTED_KEYWORD` fail to compile.  
  
Here is the list:  
  - `<boost/accumulators/accumulators_fwd.hpp>`  
  - `<boost/accumulators/statistics/tail.hpp>`  
  - `<boost/accumulators/statistics/p_square_cumul_dist.hpp>`  
  - `<boost/accumulators/statistics/peaks_over_threshold.hpp>`  
  - `<boost/accumulators/statistics/density.hpp>`  
  - `<boost/accumulators/statistics/rolling_window.hpp>`  
  - `<boost/accumulators/statistics/extended_p_square.hpp>`  
  
They are all in `accumulators`, but the error seems to be in `boost/parameter/nested_keyword.hpp` from `parameter`.

---

## Comment 6

> Username: CromwellEnage  
> Created at: 2019-05-15 05:58:44 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-492515271  

What version of GCC are you using?  What happens when you add `-DBOOST_PARAMETER_DISABLE_MP11_USAGE` to your command line arguments?

---

## Comment 7

> Username: lrineau  
> Created at: 2019-05-15 07:20:53 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-492535641  

My GCC version is the one of Linux Fedora 30:  
> gcc version 9.1.1 20190503 (Red Hat 9.1.1-1) (GCC)   
  
And unfortunately, adding `-DBOOST_PARAMETER_DISABLE_MP11_USAGE` does not change anything: I have the exact same compilation error.  
```shellsession  
[lrineau@bonnard]~/Git/boost/libs/accumulators% g++ -DBOOST_PARAMETER_DISABLE_MP11_USAGE -I ../../install/include -fsyntax-only -xc++ include/boost/accumulators/statistics/density.hpp  
In file included from include/boost/accumulators/statistics/density.hpp:16:  
../../install/include/boost/parameter/keyword.hpp: In instantiation of ‘struct boost::parameter::keyword<boost::accumulators::tag::density_cache_size_<> >’:  
include/boost/accumulators/statistics/density.hpp:37:1:   required from here  
../../install/include/boost/parameter/keyword.hpp:99:13: error: no type named ‘qualifier’ in ‘struct boost::accumulators::tag::density_cache_size_<>’  
   99 |             operator=(T const& x) const  
      |             ^~~~~~~~  
```

---

## Comment 8

> Username: CromwellEnage  
> Created at: 2019-05-15 12:30:59 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-492634398  

Okay, I'll start looking for a gcc-9 workaround.

---

## Comment 9

> Username: CromwellEnage  
> Created at: 2019-05-17 01:35:18 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-493286802  

Hmmm, I haven't been able to reproduce the error in my macOS environment.  Here's what I've done:  
  
1. Installed `gcc-9.1.0` via <https://solarianprogrammer.com/2017/05/21/compiling-gcc-macos/>  
2. Installed the Boost 1.70 headers to `/usr/local/include`  
3. Cloned the Boost.Parameter Git repo `develop` branch to subdirectory `boost-parameter`  
4. Cloned the Boost.Accumulators Git repo `develop` branch to subdirectory `boost-accumulators`  
5. Run `g++-9.1 -I./boost-accumulators/include -I./boost-parameter/include -I/usr/local/include -fsyntax-only ./boost-accumulators/include/boost/accumulators/statistics/density.hpp`  
  
I encountered no errors as a result.  
  
I notice that you have one -I option in your command, but I don't know how you've consolidated the various repos to `../../install/include`.

---

## Comment 10

> Username: lrineau  
> Created at: 2019-05-17 12:41:32 UTC  
> Url: https://github.com/boostorg/accumulators/issues/31#issuecomment-493439092  

[**@CromwellEnage**](https://github.com/CromwellEnage) commented on [May 17, 2019, 3:35 AM GMT+2](https://github.com/boostorg/accumulators/issues/31#issuecomment-493286802 "2019-05-17T01:35:18Z - Replied by Github Reply Comments"):  
> I notice that you have one -I option in your command, but I don't know how you've consolidated the various repos to `../../install/include`.  
  
You are right, and I am glad you are: I use the modular Git with submodules (https://github.com/boostorg/boost), and I trusted `./b2 install` to refresh by set of headers. I have removed all headers, relaunch `./b2 install`, and then the compilation issue is gone. Thanks for your patience. This issue is "not-a-bug".
