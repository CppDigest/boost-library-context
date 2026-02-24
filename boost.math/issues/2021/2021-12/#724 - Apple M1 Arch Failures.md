# #724 - Apple M1 Arch Failures [Closed]

> Username: mborland  
> Created at: 2021-12-12 10:36:38 UTC  
> Updated at: 2021-12-22 18:29:57 UTC  
> Closed at: 2021-12-22 18:29:57 UTC  
> Url: https://github.com/boostorg/math/issues/724  

`test_bessel_j`, `test_root_iterations`, and `test_bessel_i_prime` all fail on Apple M1 architecture. Below is a dump of the log:  
  
```  
testing.capture-output ../../../bin.v2/libs/math/test/test_bessel_j.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_j.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Tests run with Clang version 13.0.0 (clang-1300.0.29.3), libc++ version 12000, Mac OS  
Testing Bessel J0: Mathworld Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel J0: Mathworld Data (Tricky cases) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel J1: Mathworld Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel J1: Mathworld Data (tricky cases) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel JN: Mathworld Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<float> Max = 0.6104 RMS Mean=0.148  
    worst case at row: 3  
    { 2, 0.0099999998, 1.2499896e-05 }  
  
  
Testing Bessel J0: Mathworld Data (Integer Version) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel J0: Mathworld Data (Tricky cases) (Integer Version) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel J1: Mathworld Data (Integer Version) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel J1: Mathworld Data (tricky cases) (Integer Version) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel JN: Mathworld Data (Integer Version) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<float> Max = 0.6104 RMS Mean=0.148  
    worst case at row: 3  
    { 2, 0.0099999998, 1.2499896e-05 }  
  
  
Testing Bessel J: Mathworld Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<float> Max = 12.88 RMS Mean=2.88  
    worst case at row: 18  
    { 8.5, 12.566371, 0.043680795 }  
  
  
Testing Bessel JN: Random Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel J: Random Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel J: Random Data (Tricky large values) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel j: Random Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
sph_bessel<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel J0: Mathworld Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 3.777 RMS Mean=1.576  
    worst case at row: 3  
    { 0, 4, -0.39714980986384735 }  
  
  
Testing Bessel J0: Mathworld Data (Tricky cases) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 1.002e+07 RMS Mean=4.093e+06  
    worst case at row: 5  
    { 0, 11.791534423828125, -3.5301714077822377e-09 }  
  
  
Testing Bessel J1: Mathworld Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 15.98 RMS Mean=5.773  
    worst case at row: 4  
    { 1, -8, -0.23463634685391463 }  
  
  
Testing Bessel J1: Mathworld Data (tricky cases) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 6.775e+05 RMS Mean=3.03e+05  
    worst case at row: 4  
    { 1, 10.173467636108398, 1.2459133109719191e-07 }  
  
  
Testing Bessel JN: Mathworld Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 6.088 RMS Mean=1.863  
    worst case at row: 15  
    { 1000, 100000, 0.0012831781125024803 }  
  
  
Testing Bessel J0: Mathworld Data (Integer Version) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<double> Max = 3.777 RMS Mean=1.576  
    worst case at row: 3  
    { 0, 4, -0.39714980986384735 }  
  
  
Testing Bessel J0: Mathworld Data (Tricky cases) (Integer Version) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<double> Max = 1.002e+07 RMS Mean=4.093e+06  
    worst case at row: 5  
    { 0, 11.791534423828125, -3.5301714077822377e-09 }  
  
  
Testing Bessel J1: Mathworld Data (Integer Version) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<double> Max = 15.98 RMS Mean=5.773  
    worst case at row: 4  
    { 1, -8, -0.23463634685391463 }  
  
  
Testing Bessel J1: Mathworld Data (tricky cases) (Integer Version) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<double> Max = 6.775e+05 RMS Mean=3.03e+05  
    worst case at row: 4  
    { 1, 10.173467636108398, 1.2459133109719191e-07 }  
  
  
Testing Bessel JN: Mathworld Data (Integer Version) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<double> Max = 6.088 RMS Mean=1.863  
    worst case at row: 15  
    { 1000, 100000, 0.0012831781125024803 }  
  
  
Testing Bessel J: Mathworld Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 2.762 RMS Mean=0.8699  
    worst case at row: 17  
    { 1.5, 7.845703125, 0.033947764636971058 }  
  
  
Testing Bessel J: Mathworld Data (large values) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 5.899 RMS Mean=3.77  
    worst case at row: 3  
    { -2.5, 4, -0.0145679476685218 }  
  
  
Testing Bessel JN: Random Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 17.53 RMS Mean=1.438  
    worst case at row: 83  
    { 7, 31.483119964599609, -0.008465752063992112 }  
  
  
Testing Bessel J: Random Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 9.243 RMS Mean=1.342  
    worst case at row: 278  
    { 2.3767638206481934, 15.719108581542969, 0.074383474449976478 }  
  
  
Testing Bessel J: Random Data (Tricky large values) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<double> Max = 59.19 RMS Mean=8.66  
    worst case at row: 48  
    { 58.72027587890625, 489.82421875, -0.0081839655500653159 }  
  
  
Testing Bessel j: Random Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
sph_bessel<double> Max = 244.8 RMS Mean=16.3  
    worst case at row: 331  
    { 40, 125.28044128417969, -0.00051257860453121965 }  
  
  
Testing Bessel J0: Mathworld Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 3.777 RMS Mean=1.576  
    worst case at row: 3  
    { 0, 4, -0.39714980986384735 }  
  
  
Testing Bessel J0: Mathworld Data (Tricky cases) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 1.002e+07 RMS Mean=4.093e+06  
    worst case at row: 5  
    { 0, 11.791534423828125, -3.5301714077822377e-09 }  
  
  
Testing Bessel J1: Mathworld Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 15.98 RMS Mean=5.773  
    worst case at row: 4  
    { 1, -8, -0.23463634685391463 }  
  
  
Testing Bessel J1: Mathworld Data (tricky cases) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 6.775e+05 RMS Mean=3.03e+05  
    worst case at row: 4  
    { 1, 10.173467636108398, 1.2459133109719191e-07 }  
  
  
Testing Bessel JN: Mathworld Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 6.088 RMS Mean=1.863  
    worst case at row: 15  
    { 1000, 100000, 0.0012831781125024803 }  
  
  
Testing Bessel J0: Mathworld Data (Integer Version) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<long double> Max = 3.777 RMS Mean=1.576  
    worst case at row: 3  
    { 0, 4, -0.39714980986384735 }  
  
  
Testing Bessel J0: Mathworld Data (Tricky cases) (Integer Version) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<long double> Max = 1.002e+07 RMS Mean=4.093e+06  
    worst case at row: 5  
    { 0, 11.791534423828125, -3.5301714077822377e-09 }  
  
  
Testing Bessel J1: Mathworld Data (Integer Version) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<long double> Max = 15.98 RMS Mean=5.773  
    worst case at row: 4  
    { 1, -8, -0.23463634685391463 }  
  
  
Testing Bessel J1: Mathworld Data (tricky cases) (Integer Version) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<long double> Max = 6.775e+05 RMS Mean=3.03e+05  
    worst case at row: 4  
    { 1, 10.173467636108398, 1.2459133109719191e-07 }  
  
  
Testing Bessel JN: Mathworld Data (Integer Version) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<long double> Max = 6.088 RMS Mean=1.863  
    worst case at row: 15  
    { 1000, 100000, 0.0012831781125024803 }  
  
  
Testing Bessel J: Mathworld Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 2.762 RMS Mean=0.8699  
    worst case at row: 17  
    { 1.5, 7.845703125, 0.033947764636971058 }  
  
  
Testing Bessel J: Mathworld Data (large values) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 5.899 RMS Mean=3.77  
    worst case at row: 3  
    { -2.5, 4, -0.0145679476685218 }  
  
  
Testing Bessel JN: Random Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 17.53 RMS Mean=1.438  
    worst case at row: 83  
    { 7, 31.483119964599609, -0.008465752063992112 }  
  
  
Testing Bessel J: Random Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 9.243 RMS Mean=1.342  
    worst case at row: 278  
    { 2.3767638206481934, 15.719108581542969, 0.074383474449976478 }  
  
  
Testing Bessel J: Random Data (Tricky large values) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<long double> Max = 59.19 RMS Mean=8.66  
    worst case at row: 48  
    { 58.72027587890625, 489.82421875, -0.0081839655500653159 }  
  
  
Testing Bessel j: Random Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
sph_bessel<long double> Max = 244.8 RMS Mean=16.3  
    worst case at row: 331  
    { 40, 125.28044128417969, -0.00051257860453121965 }  
  
  
Testing Bessel J0: Mathworld Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 1.456 RMS Mean=0.686  
    worst case at row: 4  
    { 0, -8, 0.1716508071375539012937 }  
  
  
Testing Bessel J0: Mathworld Data (Tricky cases) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 9.264e+07 RMS Mean=3.784e+07  
    worst case at row: 5  
    { 0, 11.791534423828125, -3.530171407782237680097e-09 }  
  
  
Testing Bessel J1: Mathworld Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 7.188 RMS Mean=3.536  
    worst case at row: 7  
    { 1, -10, -0.04347274616886143833172 }  
  
  
Testing Bessel J1: Mathworld Data (tricky cases) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 3.72e+06 RMS Mean=1.677e+06  
    worst case at row: 4  
    { 1, 10.1734676361083984375, 1.245913310971919121468e-07 }  
Peak error greater than expected value of 3000000  
./handle_test_result.hpp:165: error: in "test_main": check bounds.first >= max_error_found has failed  
  
  
Testing Bessel JN: Mathworld Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 6.088 RMS Mean=1.727  
    worst case at row: 15  
    { 1000, 100000, 0.001283178112502480257978 }  
  
  
Testing Bessel J0: Mathworld Data (Integer Version) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<real_concept> Max = 1.456 RMS Mean=0.686  
    worst case at row: 4  
    { 0, -8, 0.1716508071375539012937 }  
  
  
Testing Bessel J0: Mathworld Data (Tricky cases) (Integer Version) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<real_concept> Max = 9.264e+07 RMS Mean=3.784e+07  
    worst case at row: 5  
    { 0, 11.791534423828125, -3.530171407782237680097e-09 }  
  
  
Testing Bessel J1: Mathworld Data (Integer Version) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<real_concept> Max = 7.188 RMS Mean=3.536  
    worst case at row: 7  
    { 1, -10, -0.04347274616886143833172 }  
  
  
Testing Bessel J1: Mathworld Data (tricky cases) (Integer Version) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<real_concept> Max = 3.72e+06 RMS Mean=1.677e+06  
    worst case at row: 4  
    { 1, 10.1734676361083984375, 1.245913310971919121468e-07 }  
Peak error greater than expected value of 3000000  
./handle_test_result.hpp:165: error: in "test_main": check bounds.first >= max_error_found has failed  
  
  
Testing Bessel JN: Mathworld Data (Integer Version) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j (integer orders)<real_concept> Max = 6.088 RMS Mean=1.727  
    worst case at row: 15  
    { 1000, 100000, 0.001283178112502480257978 }  
  
  
Testing Bessel J: Mathworld Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 2.762 RMS Mean=0.955  
    worst case at row: 17  
    { 1.5, 7.845703125, 0.03394776463697105756134 }  
  
  
Testing Bessel J: Mathworld Data (large values) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 5.899 RMS Mean=2.964  
    worst case at row: 3  
    { -2.5, 4, -0.01456794766852179991157 }  
  
  
Testing Bessel JN: Random Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 13.84 RMS Mean=1.771  
    worst case at row: 83  
    { 7, 31.483119964599609375, -0.008465752063992111955959 }  
  
  
Testing Bessel J: Random Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 9.243 RMS Mean=1.349  
    worst case at row: 278  
    { 2.376763820648193359375, 15.71910858154296875, 0.07438347444997647772702 }  
  
  
Testing Bessel J: Random Data (Tricky large values) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_j<real_concept> Max = 59.19 RMS Mean=8.668  
    worst case at row: 48  
    { 58.72027587890625, 489.82421875, -0.00818396555006531585541 }  
  
  
Testing Bessel j: Random Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
sph_bessel<real_concept> Max = 244.8 RMS Mean=16.3  
    worst case at row: 331  
    { 40, 125.2804412841796875, -0.0005125786045312196500395 }  
  
  
  
*** 2 failures are detected in the test module "Master Test Suite"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/math/test/test_bessel_j.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_j"   > "../../../bin.v2/libs/math/test/test_bessel_j.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_j.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/math/test/test_bessel_j.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_j.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/math/test/test_bessel_j.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_j.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/math/test/test_bessel_j.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_j.output" "../../../bin.v2/libs/math/test/test_bessel_j.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_j.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/math/test/test_bessel_j.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_j.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/math/test/test_bessel_j.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_j.run...  
testing.capture-output ../../../bin.v2/libs/math/test/test_root_iterations.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_root_iterations.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
test_root_iterations.cpp:305: error: in "test_main": check iters <= 40 has failed [51 > 40]  
  
*** 1 failure is detected in the test module "Master Test Suite"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/math/test/test_root_iterations.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_root_iterations"   > "../../../bin.v2/libs/math/test/test_root_iterations.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_root_iterations.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/math/test/test_root_iterations.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_root_iterations.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/math/test/test_root_iterations.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_root_iterations.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/math/test/test_root_iterations.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_root_iterations.output" "../../../bin.v2/libs/math/test/test_root_iterations.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_root_iterations.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/math/test/test_root_iterations.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_root_iterations.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/math/test/test_root_iterations.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_root_iterations.run...  
testing.capture-output ../../../bin.v2/libs/math/test/test_bessel_i_prime.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_i_prime.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
Tests run with Clang version 13.0.0 (clang-1300.0.29.3), libc++ version 12000, Mac OS  
Testing Bessel I'0: Mathworld Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel I'1: Mathworld Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel I'n: Mathworld Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel I'0: Mathworld Data (Integer Version) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel I'1: Mathworld Data (Integer Version) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel I'n: Mathworld Data (Integer Version) with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel I'v: Mathworld Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel I'n: Random Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel I'v: Random Data with type float  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<float> Max = 0 RMS Mean=0  
  
  
Testing Bessel I'0: Mathworld Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<double> Max = 0.8203 RMS Mean=0.3545  
    worst case at row: 4  
    { 0, -7, -156.03909286995545 }  
  
  
Testing Bessel I'1: Mathworld Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<double> Max = 1.356 RMS Mean=0.7819  
    worst case at row: 4  
    { 1, -8, 377.57997362398476 }  
  
  
Testing Bessel I'n: Mathworld Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<double> Max = 3.61 RMS Mean=1.223  
    worst case at row: 8  
    { 100, 80, 743545006.37446702 }  
  
  
Testing Bessel I'0: Mathworld Data (Integer Version) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<double> Max = 0.8203 RMS Mean=0.3545  
    worst case at row: 4  
    { 0, -7, -156.03909286995545 }  
  
  
Testing Bessel I'1: Mathworld Data (Integer Version) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<double> Max = 1.356 RMS Mean=0.7819  
    worst case at row: 4  
    { 1, -8, 377.57997362398476 }  
  
  
Testing Bessel I'n: Mathworld Data (Integer Version) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<double> Max = 3.61 RMS Mean=1.223  
    worst case at row: 8  
    { 100, 80, 743545006.37446702 }  
  
  
Testing Bessel I'v: Mathworld Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<double> Max = 3763 RMS Mean=1190  
    worst case at row: 2  
    { -4.9990234375, 2.125, 0.00011319255598711991 }  
Peak error greater than expected value of 3500  
./handle_test_result.hpp:165: error: in "test_main": check bounds.first >= max_error_found has failed  
  
  
Testing Bessel I'n: Random Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<double> Max = 9.849 RMS Mean=1.823  
    worst case at row: 1946  
    { -82, -24.750102996826172, -1.752033409073499e-32 }  
  
  
Testing Bessel I'v: Random Data with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<double> Max = 13.99 RMS Mean=2.462  
    worst case at row: 395  
    { -98.576263427734375, 0.40808981657028198, -1.1164173761672894e+223 }  
  
  
Testing Bessel I'v: Mathworld Data (large values) with type double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<double> Max = 59.53 RMS Mean=26.62  
    worst case at row: 3  
    { 0.5, 1.2458993688871959e-206, 3.5741154998461287e+102 }  
  
  
Testing Bessel I'0: Mathworld Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<long double> Max = 0.8203 RMS Mean=0.3545  
    worst case at row: 4  
    { 0, -7, -156.03909286995545 }  
  
  
Testing Bessel I'1: Mathworld Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<long double> Max = 1.356 RMS Mean=0.7819  
    worst case at row: 4  
    { 1, -8, 377.57997362398476 }  
  
  
Testing Bessel I'n: Mathworld Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<long double> Max = 3.61 RMS Mean=1.223  
    worst case at row: 8  
    { 100, 80, 743545006.37446702 }  
  
  
Testing Bessel I'0: Mathworld Data (Integer Version) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<long double> Max = 0.8203 RMS Mean=0.3545  
    worst case at row: 4  
    { 0, -7, -156.03909286995545 }  
  
  
Testing Bessel I'1: Mathworld Data (Integer Version) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<long double> Max = 1.356 RMS Mean=0.7819  
    worst case at row: 4  
    { 1, -8, 377.57997362398476 }  
  
  
Testing Bessel I'n: Mathworld Data (Integer Version) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<long double> Max = 3.61 RMS Mean=1.223  
    worst case at row: 8  
    { 100, 80, 743545006.37446702 }  
  
  
Testing Bessel I'v: Mathworld Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<long double> Max = 3763 RMS Mean=1190  
    worst case at row: 2  
    { -4.9990234375, 2.125, 0.00011319255598711991 }  
Peak error greater than expected value of 3500  
./handle_test_result.hpp:165: error: in "test_main": check bounds.first >= max_error_found has failed  
  
  
Testing Bessel I'n: Random Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<long double> Max = 9.849 RMS Mean=1.823  
    worst case at row: 1946  
    { -82, -24.750102996826172, -1.752033409073499e-32 }  
  
  
Testing Bessel I'v: Random Data with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<long double> Max = 13.99 RMS Mean=2.462  
    worst case at row: 395  
    { -98.576263427734375, 0.40808981657028198, -1.1164173761672894e+223 }  
  
  
Testing Bessel I'v: Mathworld Data (large values) with type long double  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<long double> Max = 59.53 RMS Mean=26.62  
    worst case at row: 3  
    { 0.5, 1.2458993688871959e-206, 3.5741154998461287e+102 }  
  
  
Testing Bessel I'0: Mathworld Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<real_concept> Max = 4.922 RMS Mean=1.926  
    worst case at row: 4  
    { 0, -7, -156.0390928699554535797 }  
  
  
Testing Bessel I'1: Mathworld Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<real_concept> Max = 3.063 RMS Mean=1.306  
    worst case at row: 9  
    { 1, 200, 2.029514265663064192627e+85 }  
  
  
Testing Bessel I'n: Mathworld Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<real_concept> Max = 3.61 RMS Mean=1.223  
    worst case at row: 8  
    { 100, 80, 743545006.3744670152664 }  
  
  
Testing Bessel I'0: Mathworld Data (Integer Version) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<real_concept> Max = 4.922 RMS Mean=1.926  
    worst case at row: 4  
    { 0, -7, -156.0390928699554535797 }  
  
  
Testing Bessel I'1: Mathworld Data (Integer Version) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<real_concept> Max = 3.063 RMS Mean=1.306  
    worst case at row: 9  
    { 1, 200, 2.029514265663064192627e+85 }  
  
  
Testing Bessel I'n: Mathworld Data (Integer Version) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime (integer orders)<real_concept> Max = 3.61 RMS Mean=1.223  
    worst case at row: 8  
    { 100, 80, 743545006.3744670152664 }  
  
  
Testing Bessel I'v: Mathworld Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<real_concept> Max = 3763 RMS Mean=1190  
    worst case at row: 2  
    { -4.9990234375, 2.125, 0.000113192555987119905926 }  
  
  
Testing Bessel I'n: Random Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<real_concept> Max = 9.849 RMS Mean=1.836  
    worst case at row: 1946  
    { -82, -24.750102996826171875, -1.752033409073498993383e-32 }  
  
  
Testing Bessel I'v: Random Data with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<real_concept> Max = 13.99 RMS Mean=2.491  
    worst case at row: 395  
    { -98.576263427734375, 0.4080898165702819824219, -1.116417376167289422972e+223 }  
  
  
Testing Bessel I'v: Mathworld Data (large values) with type real_concept  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
cyl_bessel_i_prime<real_concept> Max = 58.28 RMS Mean=26.07  
    worst case at row: 3  
    { 0.5, 1.245899368887195941939e-206, 3.574115499846128650918e+102 }  
  
  
  
*** 2 failures are detected in the test module "Master Test Suite"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/math/test/test_bessel_i_prime.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_i_prime"   > "../../../bin.v2/libs/math/test/test_bessel_i_prime.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_i_prime.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/math/test/test_bessel_i_prime.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_i_prime.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/math/test/test_bessel_i_prime.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_i_prime.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/math/test/test_bessel_i_prime.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_i_prime.output" "../../../bin.v2/libs/math/test/test_bessel_i_prime.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_i_prime.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/math/test/test_bessel_i_prime.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_i_prime.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/math/test/test_bessel_i_prime.test/clang-darwin-13/debug/cxxstd-11-iso/link-static/threading-multi/visibility-hidden/test_bessel_i_prime.run...  
...failed updating 3 targets...  
...skipped 3 targets...  
```  
  
Errors are not limited to long double oddity like on Apple with x86_64. I can look into some of the older assumptions being made about Apple in config that may not apply to M1.

---

## Comment 1

> Username: NAThompson  
> Created at: 2021-12-12 16:19:26 UTC  
> Url: https://github.com/boostorg/math/issues/724#issuecomment-991926619  

Well I think there's some long double arg promotion that on x86 will actually improve accuracy but on M1 will be a no-op.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-12-12 16:59:49 UTC  
> Url: https://github.com/boostorg/math/issues/724#issuecomment-991933169  

I haven't created a PR for it because we have no way of running CI on M1, but can you test the referenced patch?  I've basically just updated the expected error rates - the differences are nothing that isn't seen on other platforms, so this is mostly a <shrug> and move on.  Also I'm not sure if I have M1 platform detection correct: I've used `__arm__` for this, but that may not be correct?

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-12-13 01:54:59 UTC  
> Url: https://github.com/boostorg/math/issues/724#issuecomment-992039528  

> Also I'm not sure if I have M1 platform detection correct: I've used __arm__ for this, but that may not be correct?  
  
If the assumption is that these failures are coming from `long double` being 64 bit, then I think this is correct.
