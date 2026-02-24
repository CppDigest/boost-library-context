# #879 - Error when building Valhalla using boost.geometry [Closed]

> Username: chrstnbwnkl  
> Created at: 2021-07-02 12:38:54 UTC  
> Updated at: 2021-07-02 17:54:48 UTC  
> Closed at: 2021-07-02 12:43:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/879  

While building [valhalla](https://github.com/valhalla/valhalla) the current `brew` package for boost.geometry (1.76.0) errors with the following:  
```  
❯ make -j$(sysctl -n hw.physicalcpu)  
[  5%] Built target valhalla-proto  
[  8%] Built target valhalla-midgard  
[  8%] Built target libvalhalla.dylib  
[  8%] Built target gtest  
[  8%] Built target build_timezones  
[ 14%] Built target benchmark  
[ 20%] Built target valhalla-odin  
[ 35%] Built target valhalla-baldr  
[ 35%] Built target gmock  
[ 35%] Built target gtest_main  
[ 38%] Built target benchmark_main  
Consolidate compiler generated dependencies of target valhalla-mjolnir  
[ 38%] Built target valhalla-skadi  
[ 38%] Building CXX object src/mjolnir/CMakeFiles/valhalla-mjolnir.dir/admin.cc.o  
[ 41%] Built target valhalla-sif  
[ 44%] Built target gmock_main  
[ 44%] Building CXX object src/mjolnir/CMakeFiles/valhalla-mjolnir.dir/complexrestrictionbuilder.cc.o  
Consolidate compiler generated dependencies of target valhalla-loki  
[ 50%] Built target valhalla-loki  
[ 50%] Building CXX object src/mjolnir/CMakeFiles/valhalla-mjolnir.dir/countryaccess.cc.o  
Consolidate compiler generated dependencies of target valhalla-meili  
[ 50%] Building CXX object src/meili/CMakeFiles/valhalla-meili.dir/routing.cc.o  
In file included from /Users/christianbeiwinkel/OneDrive - Universiteit Utrecht/GIMA/Internship/dev/valhalla/src/mjolnir/admin.cc:1:  
In file included from /Users/christianbeiwinkel/OneDrive - Universiteit Utrecht/GIMA/Internship/dev/valhalla/valhalla/mjolnir/admin.h:4:  
In file included from /usr/local/include/boost/geometry.hpp:17:  
In file included from /usr/local/include/boost/geometry/geometry.hpp:58:  
In file included from /usr/local/include/boost/geometry/strategies/strategies.hpp:37:  
In file included from /usr/local/include/boost/geometry/strategies/intersection_strategies.hpp:26:  
In file included from /usr/local/include/boost/geometry/strategies/cartesian/intersection.hpp:30:  
In file included from /usr/local/include/boost/geometry/algorithms/detail/equals/point_point.hpp:27:  
In file included from /usr/local/include/boost/geometry/strategies/detail.hpp:15:  
In file included from /usr/local/include/boost/geometry/strategies/geographic/parameters.hpp:21:  
In file included from /usr/local/include/boost/geometry/formulas/karney_direct.hpp:44:  
/usr/local/include/boost/geometry/util/series_expansion.hpp:520:22: error: unused variable 'coeff_size' [-Werror,-Wunused-variable]  
        size_t const coeff_size = Coeffs::static_size;  
                     ^  
/usr/local/include/boost/geometry/util/series_expansion.hpp:521:22: error: unused variable 'expected_size' [-Werror,-Wunused-variable]  
        size_t const expected_size = (SeriesOrder * (SeriesOrder - 1)) / 2;  
                     ^  
2 errors generated.  
make[2]: *** [src/mjolnir/CMakeFiles/valhalla-mjolnir.dir/admin.cc.o] Error 1  
make[1]: *** [src/mjolnir/CMakeFiles/valhalla-mjolnir.dir/all] Error 2  
make[1]: *** Waiting for unfinished jobs....  
[ 50%] Building CXX object src/meili/CMakeFiles/valhalla-meili.dir/candidate_search.cc.o  
[ 50%] Building CXX object src/meili/CMakeFiles/valhalla-meili.dir/transition_cost_model.cc.o  
[ 50%] Building CXX object src/meili/CMakeFiles/valhalla-meili.dir/map_matcher.cc.o  
[ 50%] Building CXX object src/meili/CMakeFiles/valhalla-meili.dir/map_matcher_factory.cc.o  
[ 50%] Building CXX object src/meili/CMakeFiles/valhalla-meili.dir/match_route.cc.o  
[ 52%] Built target valhalla-meili  
make: *** [all] Error 2  
  
```  
  
System: mac os version 10.15.7  
Compiler: `Configured with: --prefix=/Library/Developer/CommandLineTools/usr --with-gxx-include-dir=/Library/Developer/CommandLineTools/SDKs/MacOSX10.15.sdk/usr/include/c++/4.2.1  
Apple clang version 12.0.0 (clang-1200.0.32.29)  
Target: x86_64-apple-darwin19.6.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin`

---

## Comment 1

> Username: mloskot  
> Created at: 2021-07-02 12:43:30 UTC  
> Updated at: 2021-07-02 12:46:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/879#issuecomment-872969300  

First, this is not an error but warning treated as error  
```  
error: unused variable 'coeff_size' [-Werror,-Wunused-variable]  
        size_t const coeff_size = Coeffs::static_size;  
```  
  
Second, this is due to Valhalla build configuration, namely, forcing `-Werror,-Wunused-variable` flags.  
  
Third, this is not Boost.Geometry problem, so you should rather open an issue for Valhalla asking to allow less strict compilation mode (flags) for Valhalla non-contributors / end users.  
  
------  
  
See Valhalla's https://github.com/valhalla/valhalla/pull/2937 and its CMake options:  
- https://github.com/valhalla/valhalla/blob/58c8d9fca516cae859415dd2407ca1f3bc36f162/CMakeLists.txt#L36  
- https://github.com/valhalla/valhalla/blob/58c8d9fca516cae859415dd2407ca1f3bc36f162/CMakeLists.txt#L33  
  
Or reach Valhalla community with questions at https://github.com/valhalla/valhalla/discussions

---

## Comment 2

> Username: nilsnolde  
> Created at: 2021-07-02 13:37:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/879#issuecomment-873004765  

@mloskot I put up @chrstnbwnkl to write this issue (he just started with us, unfortunately on a mac;)).   
  
For context, I had a similar [issue](https://github.com/valhalla/valhalla/issues/3157) on valhalla with googletest lately and figured it was a problem with their cmake file which also sets `-Werror` and the cause (uninitialized vars) was fixed in a later release. Valhalla's options didn't take any effect there, we had to upgrade gtest.  
  
Anyways, wasn't thinking enough about the cause of this, obviously bg can't set compiler directives when it's simply installed. Sorry for the bother, we'll continue testing and reporting in valhalla's repo..

---

## Comment 3

> Username: mloskot  
> Created at: 2021-07-02 15:38:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/879#issuecomment-873088105  

@nilsnolde No worries.   
  
------  
  
From Valhalla point, a possible solution in Valhalla could be to distinguish developer and end-user builds.  
Here is how Kitware does it, using CMake targets trick, in their VTK-m:  
https://github.com/Kitware/VTK-m/blob/cb3bb43ff936249c213daabd35b3c36d2e7af04c/CMakeLists.txt#L145-L148  
In fact, I copied their solution to GEOS a while ago  
https://github.com/libgeos/geos/blob/133cd9f88a410e068704f52db78a0d8f3853d89c/CMakeLists.txt#L208-L227

---

## Comment 4

> Username: nilsnolde  
> Created at: 2021-07-02 16:05:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/879#issuecomment-873104869  

Perfect thanks, I’ll give that a read!

---

## Comment 5

> Username: awulkiew  
> Created at: 2021-07-02 17:51:07 UTC  
> Updated at: 2021-07-02 17:54:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/879#issuecomment-873163404  

Thanks for reporting the issue @chrstnbwnkl and for handling it @mloskot !  
  
While this compilation failure is not an error in Geometry we do fix compiler warnings. So if you see them please report them @nilsnolde. :)  
  
This one was already fixed:  
https://github.com/boostorg/geometry/commit/1a3f4255831115f8cd8afaa28279fd43f6395d9a  
and will be released with Boost-1.77
