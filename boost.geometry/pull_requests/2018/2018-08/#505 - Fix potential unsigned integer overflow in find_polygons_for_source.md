# #505 Fix potential unsigned integer overflow in find_polygons_for_source [Merged]

> Username: springmeyer  
> Created at: 2018-08-11 16:02:10 UTC  
> Updated at: 2018-10-12 13:36:05 UTC  
> Merged at: 2018-08-13 14:36:07 UTC  
> Closed at: 2018-08-13 14:36:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/505  

An invalid polygon will trigger overflow when `previous_rank` is `0` as `previous_rank - 1` will overflow. This can be detected by passing an invalid polygon like `[[8128,3600],[8224,3664],[8128,3600]]` into `boost::geometry::intersection` and compiling with `-fsanitize=undefined`.  
  
I'm aware that `boost::geometry::intersection` is not meant to be used with invalid polygons. However I still think that an overflow is not desirable. Better to prevent it or (if others think this fix is incorrect) perhaps an exception would be better?

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-08-11 17:21:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-412289565  

A test case would be helpful

---

## Comment 2

> Username: springmeyer  
> Created_at: 2018-08-12 16:16:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-412353455  

> A test case would be helpful  
  
I agree. First, before testing my changes or writing a new testcase, I ran the tests locally against the `develop` branch at 319ee31d6ac on OS X 10.13.6 with:  
  
```  
$ clang++ -v  
Apple LLVM version 9.1.0 (clang-902.0.39.1)  
Target: x86_64-apple-darwin17.7.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```  
  
Unfortunately I see (again this is with an unchanged `develop` branch):  
   
```  
$ time ../../b2 test  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...patience...  
...patience...  
...patience...  
...found 11165 targets...  
...updating 8 targets...  
testing.capture-output ../../bin.v2/libs/geometry/test/algorithms/envelope_expand/algorithms_envelope_on_spheroid.test/darwin-4.2.1/debug/algorithms_envelope_on_spheroid.run  
====== BEGIN OUTPUT ======  
Running 24 test cases...  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid": case ID: s11, MBR units: degrees; geometry: SEGMENT((260, 30), (20, 45)); expected: (-100 30, 20 [57.990810958016482, 57.990810958016965]), detected: ((-100, 30), (20, 57.990810958016468))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid": case ID: s11-reverse, MBR units: degrees; geometry: SEGMENT((260, 30), (20, 45)); expected: (-100 30, 20 [57.990810958016482, 57.990810958016965]), detected: ((-100, 30), (20, 57.990810958016468))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_thomas": case ID: s11, MBR units: degrees; geometry: SEGMENT((260, 30), (20, 45)); expected: (-100 30, 20 [57.990810958016482, 57.990810958016965]), detected: ((-100, 30), (20, 57.990810958016468))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_thomas": case ID: s11-reverse, MBR units: degrees; geometry: SEGMENT((260, 30), (20, 45)); expected: (-100 30, 20 [57.990810958016482, 57.990810958016965]), detected: ((-100, 30), (20, 57.990810958016468))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s02, MBR units: degrees; geometry: SEGMENT((10, 10), (40, 10)); expected: (10 10, 40 10.34758709960203), detected: ((10, 10), (40, 10.347587099602027))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s02-reverse, MBR units: degrees; geometry: SEGMENT((10, 10), (40, 10)); expected: (10 10, 40 10.34758709960203), detected: ((10, 10), (40, 10.347587099602027))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s02a, MBR units: degrees; geometry: SEGMENT((40, 10), (10, 10)); expected: (10 10, 40 10.34758709960203), detected: ((10, 10), (40, 10.347587099602027))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s02a-reverse, MBR units: degrees; geometry: SEGMENT((40, 10), (10, 10)); expected: (10 10, 40 10.34758709960203), detected: ((10, 10), (40, 10.347587099602027))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s03, MBR units: degrees; geometry: SEGMENT((160, 10), (-170, 10)); expected: (160 10, 190 10.34758709960203), detected: ((160, 10), (190, 10.347587099602027))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s03-reverse, MBR units: degrees; geometry: SEGMENT((160, 10), (-170, 10)); expected: (160 10, 190 10.34758709960203), detected: ((160, 10), (190, 10.347587099602027))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s03a, MBR units: degrees; geometry: SEGMENT((-170, 10), (160, 10)); expected: (160 10, 190 10.34758709960203), detected: ((160, 10), (190, 10.347587099602027))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s03a-reverse, MBR units: degrees; geometry: SEGMENT((-170, 10), (160, 10)); expected: (160 10, 190 10.34758709960203), detected: ((160, 10), (190, 10.347587099602027))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s03b, MBR units: degrees; geometry: SEGMENT((-170, -10), (160, -10)); expected: (160 -10.34758709960203, 190 -10), detected: ((160, -10.347587099602027), (190, -10))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s03b-reverse, MBR units: degrees; geometry: SEGMENT((-170, -10), (160, -10)); expected: (160 -10.34758709960203, 190 -10), detected: ((160, -10.347587099602027), (190, -10))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s11, MBR units: degrees; geometry: SEGMENT((260, 30), (20, 45)); expected: (-100 30, 20 [57.990742552279649, 57.990742552280153]), detected: ((-100, 30), (20, 57.990742552279634))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_segment_spheroid_with_strategy_andoyer": case ID: s11-reverse, MBR units: degrees; geometry: SEGMENT((260, 30), (20, 45)); expected: (-100 30, 20 [57.990742552279649, 57.990742552280153]), detected: ((-100, 30), (20, 57.990742552279634))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_spheroid_linestring": case ID: l09, MBR units: degrees; geometry: LINESTRING(-40 20,-40 20,-140 85,-10 5,-10 5); expected: (-140 5, -10 86.18564770636192), detected: ((-140, 5), (-10, 86.185647706361891))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_spheroid_linestring": case ID: l09, MBR units: radians; geometry: LINESTRING(-40 20,-40 20,-140 85,-10 5,-10 5); expected: (-2.4434609527920612 0.087266462599716474, -0.17453292519943295 1.5042233204399145), detected: ((-2.4434609527920612, 0.087266462599716474), (-0.17453292519943295, 1.504223320439914))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_spheroid_linestring": case ID: l09-reverse, MBR units: degrees; geometry: LINESTRING(-10 5,-10 5,-140 85,-40 20,-40 20); expected: (-140 5, -10 86.18564770636192), detected: ((-140, 5), (-10, 86.185647706361891))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_spheroid_linestring": case ID: l09-reverse, MBR units: radians; geometry: LINESTRING(-10 5,-10 5,-140 85,-40 20,-40 20); expected: (-2.4434609527920612 0.087266462599716474, -0.17453292519943295 1.5042233204399145), detected: ((-2.4434609527920612, 0.087266462599716474), (-0.17453292519943295, 1.504223320439914))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_spheroid_linestring": case ID: l09a, MBR units: degrees; geometry: LINESTRING(-40 20,320 20,-140 85,-10 5,350 5); expected: (-140 5, -10 86.18564770636192), detected: ((-140, 5), (-10, 86.185647706361891))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_spheroid_linestring": case ID: l09a, MBR units: radians; geometry: LINESTRING(-40 20,320 20,-140 85,-10 5,350 5); expected: (-2.4434609527920612 0.087266462599716474, -0.17453292519943295 1.5042233204399145), detected: ((-2.4434609527920612, 0.087266462599716474), (-0.17453292519943295, 1.504223320439914))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_spheroid_linestring": case ID: l09a-reverse, MBR units: degrees; geometry: LINESTRING(350 5,-10 5,-140 85,320 20,-40 20); expected: (-140 5, -10 86.18564770636192), detected: ((-140, 5), (-10, 86.185647706361891))  
test/algorithms/envelope_expand/envelope_on_spheroid.cpp:182: error: in "envelope_spheroid_linestring": case ID: l09a-reverse, MBR units: radians; geometry: LINESTRING(350 5,-10 5,-140 85,320 20,-40 20); expected: (-2.4434609527920612 0.087266462599716474, -0.17453292519943295 1.5042233204399145), detected: ((-2.4434609527920612, 0.087266462599716474), (-0.17453292519943295, 1.504223320439914))  
  
*** 24 failures are detected in the test module "test_envelope_on_sphere_or_spheroid"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
      
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/geometry/test/algorithms/envelope_expand/algorithms_envelope_on_spheroid.test/darwin-4.2.1/debug/algorithms_envelope_on_spheroid"   > "../../bin.v2/libs/geometry/test/algorithms/envelope_expand/algorithms_envelope_on_spheroid.test/darwin-4.2.1/debug/algorithms_envelope_on_spheroid.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/geometry/test/algorithms/envelope_expand/algorithms_envelope_on_spheroid.test/darwin-4.2.1/debug/algorithms_envelope_on_spheroid.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/geometry/test/algorithms/envelope_expand/algorithms_envelope_on_spheroid.test/darwin-4.2.1/debug/algorithms_envelope_on_spheroid.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/geometry/test/algorithms/envelope_expand/algorithms_envelope_on_spheroid.test/darwin-4.2.1/debug/algorithms_envelope_on_spheroid.output" "../../bin.v2/libs/geometry/test/algorithms/envelope_expand/algorithms_envelope_on_spheroid.test/darwin-4.2.1/debug/algorithms_envelope_on_spheroid.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/geometry/test/algorithms/envelope_expand/algorithms_envelope_on_spheroid.test/darwin-4.2.1/debug/algorithms_envelope_on_spheroid.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/geometry/test/algorithms/envelope_expand/algorithms_envelope_on_spheroid.test/darwin-4.2.1/debug/algorithms_envelope_on_spheroid.run...  
testing.capture-output ../../bin.v2/libs/geometry/test/algorithms/overlay/algorithms_get_turns_linear_linear_sph.test/darwin-4.2.1/debug/algorithms_get_turns_linear_linear_sph.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
test/algorithms/overlay/test_get_turns.hpp:214: error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) and LINESTRING(4 5,3 2,1 0,0 0) -> Expected turns #: 3 detected turns #: 4  
test/algorithms/overlay/test_get_turns.hpp:243: error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) and LINESTRING(4 5,3 2,1 0,0 0) -> Expected turns: ecc== mix+= txi=+ Detected turns: mix+= mui=+ tui=+ txu++   
test/algorithms/overlay/test_get_turns.hpp:214: error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(4 5,3 2,1 0,0 0) and LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) -> Expected turns #: 3 detected turns #: 4  
test/algorithms/overlay/test_get_turns.hpp:243: error: in "test_main_caller( argc, argv )": get_turns: LINESTRING(4 5,3 2,1 0,0 0) and LINESTRING(-1 0,1 0,2 1.0004570537241201524198894179384922,3 2) -> Expected turns: ecc== mxi=+ tix+= Detected turns: miu+= mxi=+ tiu+= tux++   
  
*** 4 failures are detected in the test module "Test Program"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
      
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/geometry/test/algorithms/overlay/algorithms_get_turns_linear_linear_sph.test/darwin-4.2.1/debug/algorithms_get_turns_linear_linear_sph"   > "../../bin.v2/libs/geometry/test/algorithms/overlay/algorithms_get_turns_linear_linear_sph.test/darwin-4.2.1/debug/algorithms_get_turns_linear_linear_sph.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/geometry/test/algorithms/overlay/algorithms_get_turns_linear_linear_sph.test/darwin-4.2.1/debug/algorithms_get_turns_linear_linear_sph.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/geometry/test/algorithms/overlay/algorithms_get_turns_linear_linear_sph.test/darwin-4.2.1/debug/algorithms_get_turns_linear_linear_sph.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/geometry/test/algorithms/overlay/algorithms_get_turns_linear_linear_sph.test/darwin-4.2.1/debug/algorithms_get_turns_linear_linear_sph.output" "../../bin.v2/libs/geometry/test/algorithms/overlay/algorithms_get_turns_linear_linear_sph.test/darwin-4.2.1/debug/algorithms_get_turns_linear_linear_sph.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/geometry/test/algorithms/overlay/algorithms_get_turns_linear_linear_sph.test/darwin-4.2.1/debug/algorithms_get_turns_linear_linear_sph.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/geometry/test/algorithms/overlay/algorithms_get_turns_linear_linear_sph.test/darwin-4.2.1/debug/algorithms_get_turns_linear_linear_sph.run...  
testing.capture-output ../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/darwin-4.2.1/debug/strategies_segment_intersection_geo.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
test/strategies/segment_intersection_sph.hpp:117: error: in "test_main_caller( argc, argv )": IP0: POINT(-0.4999999977192058 50.0032319225803) different than expected: POINT(-0.4999999963998482 50.00323192258598) for LINESTRING(-1 50,1 50) and LINESTRING(-2 50,0 50)  
test/strategies/segment_intersection_sph.hpp:117: error: in "test_main_caller( argc, argv )": IP0: POINT(0.5000000064216233 50.0032319225803) different than expected: POINT(0.5000000051005989 50.00323192258598) for LINESTRING(-1 50,1 50) and LINESTRING(0 50,2 50)  
  
*** 2 failures are detected in the test module "Test Program"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
      
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/darwin-4.2.1/debug/strategies_segment_intersection_geo"   > "../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/darwin-4.2.1/debug/strategies_segment_intersection_geo.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/darwin-4.2.1/debug/strategies_segment_intersection_geo.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/darwin-4.2.1/debug/strategies_segment_intersection_geo.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/darwin-4.2.1/debug/strategies_segment_intersection_geo.output" "../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/darwin-4.2.1/debug/strategies_segment_intersection_geo.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/darwin-4.2.1/debug/strategies_segment_intersection_geo.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/geometry/test/strategies/strategies_segment_intersection_geo.test/darwin-4.2.1/debug/strategies_segment_intersection_geo.run...  
testing.capture-output ../../bin.v2/libs/geometry/test/strategies/strategies_vincenty.test/darwin-4.2.1/debug/strategies_vincenty.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
test/strategies/vincenty.cpp:67: error: in "test_main_caller( argc, argv )": direct_lon2_deg - the difference {1.02505e-05} between {1.02505e-05} and {0} exceeds {1e-05}  
  
*** 1 failure is detected in the test module "Test Program"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
      
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/geometry/test/strategies/strategies_vincenty.test/darwin-4.2.1/debug/strategies_vincenty"   > "../../bin.v2/libs/geometry/test/strategies/strategies_vincenty.test/darwin-4.2.1/debug/strategies_vincenty.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/geometry/test/strategies/strategies_vincenty.test/darwin-4.2.1/debug/strategies_vincenty.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/geometry/test/strategies/strategies_vincenty.test/darwin-4.2.1/debug/strategies_vincenty.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/geometry/test/strategies/strategies_vincenty.test/darwin-4.2.1/debug/strategies_vincenty.output" "../../bin.v2/libs/geometry/test/strategies/strategies_vincenty.test/darwin-4.2.1/debug/strategies_vincenty.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/geometry/test/strategies/strategies_vincenty.test/darwin-4.2.1/debug/strategies_vincenty.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../bin.v2/libs/geometry/test/strategies/strategies_vincenty.test/darwin-4.2.1/debug/strategies_vincenty.run...  
...failed updating 4 targets...  
...skipped 4 targets...  
  
real	0m5.467s  
user	0m2.637s  
sys	0m1.250s  
```  
  
I'm going to stop trying here until I can get feedback that these failures in the existing `develop` branch are expected and why.

---

## Comment 3

> Username: springmeyer  
> Created_at: 2018-08-12 21:04:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-412371614  

Going to step away from trying to add a test myself. I spent a few hours trying to figure out how to add one, but ran out of time. Recompiles were taking > 15 minutes when making changes to `side_by_side.hpp` which slowed me down in trying to figure out a better patch. Currently with the existing PR I'm seeing new errors like:  
  
```  
test/algorithms/overlay/sort_by_side_basic.cpp:202: error: in "test_main_caller( argc, argv )":   caseid=full1 open_count: expected=0 detected=1  
test/algorithms/overlay/sort_by_side_basic.cpp:212: error: in "test_main_caller( argc, argv )":   caseid=full1 right count: expected=[1 , 1 , 1 , 1] detected=[0 , 1 , 1 , 1]  
test/algorithms/overlay/sort_by_side.cpp:218: error: in "test_main_caller( argc, argv )":   caseid=case_recursive_boxes_22_union open count: expected=[1] detected=[2]  
```

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2018-08-12 21:19:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-412372442  

Hi @springmeyer ,  
Thanks a lot! I'm OK with merging this bugfix  
Regards, Barend

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2018-08-12 21:23:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-412372705  

I will look at that sort_by_side.cpp error in September. This month no time anymore from my side.  
But you can merge it, this error was apparently already there, no need to solve it for this bugfix (at least, that is my opinion).

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-08-13 07:55:36 UTC  
> Updated_at: 2018-08-13 12:58:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-412437145  

Thanks @springmeyer   
The `b2 test` runs everything and more issues from sanitizer are likely, so that may obscure the picture.  
I will see if I can check the test in myself later this week.  
  
@awulkiew Is it waiting for you to give green light, do you want to merge yourself?

---

## Comment 7

> Username: awulkiew  
> Created_at: 2018-08-13 14:36:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-412540258  

Thanks!

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2018-09-20 13:35:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-423185265  

Hi @springmeyer , @awulkiew , @mloskot   
  
This fix was unfortunately responsible for two failures in the regression matrix: intersection_multi and difference_multi  
Apparently it was not detected until now.  
I'm fixing the bug now in another way, but @springmeyer , do you have a testcase where this went wrong (so where the overflow was there?)

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2018-09-20 15:51:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-423235373  

It is fixed now  
https://github.com/boostorg/geometry/commit/50691def6bd1b957dd937a1ba85852ccbccfcb0b  
Indeed the overflow was there, the code still worked with that.  
I'll keep an eye on the regression matrix, should return to green again.

---

## Comment 10

> Username: springmeyer  
> Created_at: 2018-09-26 19:38:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/505#issuecomment-424843404  

Hi @barendgehrels - great than an improved fix landed. Sorry about my role in the the regression.  
  
> I'm fixing the bug now in another way, but @springmeyer , do you have a testcase where this went wrong (so where the overflow was there?)  
  
Per my comment at https://github.com/boostorg/geometry/pull/505#issuecomment-412371614 I tried to create a reduced testcase in boost geometry, but was unable.  
  
So, the only place I replicated was at the extreme downstream end of things. If you'd like to try to replicate this way what you would do is:  
  
```bash  
git clone https://github.com/mapbox/vtcomposite.git  
cd vtcomposite  
# ensure it builds and tests pass normally  
make && make test  
```  
  
If that works then things are working normally. Now let's ensure that the sanitize job is working normally. Do:  
  
```  
make sanitize  
```  
  
That should also pass. Now attempt to replicate:  
  
Edit https://github.com/mapbox/vtcomposite/blob/809a55a51cc93248042f8ebf29f82a77ee4a84e9/test/vtcomposite.test.js#L272 by removing the `if (!process.env.ASAN_OPTIONS) {` so that test will run. That is the one that triggered this problem.  
  
Now, after that edit, if you run this again you should hit the issue:  
  
```  
make sanitize  
[.... snip.... ]  
# [composite] resolves polygon clockwise error in overzoomed V1 tiles  
Skipping feature with malformed geometry (v1): expected command 2 but got 7  
Skipping feature with malformed geometry (v1): expected command 2 but got 7  
Skipping feature with malformed geometry (v1): expected command 2 but got 7  
Skipping feature with malformed geometry (v1): expected command 2 but got 7  
/Users/danespringmeyer/projects/vtcomposite2/mason_packages/.link/include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:525:60: runtime error: unsigned integer overflow: 0 - 1 cannot be represented in type 'unsigned long'  
    #0 0x10659f23c in void boost::geometry::detail::overlay::sort_by_side::side_sorter<true, false, (boost::geometry::overlay_type)1, mapbox::geometry::point<long long>, boost::geometry::strategy::side::side_by_triangle<void>, std::__1::less<int> >::find_polygons_for_source<&(boost::geometry::segment_identifier::source_index)>(long, unsigned long) (/Users/danespringmeyer/projects/vtcomposite2/lib/binding/vtcomposite.node:x86_64+0x2ef23c)  
  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior /Users/danespringmeyer/projects/vtcomposite2/mason_packages/.link/include/boost/geometry/algorithms/detail/overlay/sort_by_side.hpp:525:60 in   
./scripts/sanitize.sh: line 53: 27989 Abort trap: 6           DYLD_INSERT_LIBRARIES=${MASON_LLVM_RT_PRELOAD} node node_modules/.bin/tape test/*test.js  
make: *** [sanitize] Error 134  
```  
  
If you want to test a fix to boost geometry you'd need to replace vendored boost with your custom boost install. You could do that by removing the vendored boost:  
  
```  
rm -rf mason_packages/headers/boost/1.66.0/include/boost/  
```  
  
And then symlinking your boost headers in that previous location. Then running `make sanitize` again: hopefully the problem is gone.

---
